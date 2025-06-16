# Live Demonstration 2: Automating the installation and configuration of a web servers farm & HAProxy load balancer


### The manual way: Using several imperative commands to install the HA load balancer

#### Configure the HAProxy load balancer

```bash
$ ssh servera
```
```bash
$ sudo yum install haproxy
```
```bash
$ sudo vim /etc/haproxy/haproxy.cfg
```
```bash
frontend http_web *:80
    mode http
    default_backend server
…
backend rgw
    balance roundrobin
    mode http
    server  serverb 172.25.250.11:80 check
    server  serverc 172.25.250.11:80 check
```

#### Install and configure the web servers

```bash
$ ssh serverb | serverc
```
```bash
$ sudo dnf install httpd -y
```
```bash
$ echo “Welcome to the example.com intranet!\n” > /var/www/html/index.html
```
```bash
$ sudo systemctl enable –now httpd
```
```bash
$ sudo firewall-cmd –add-service=httpd
```
```bash
$ sudo firewall-cmd –add-service=httpd –permanent 
```

### The automatic way: Using an Ansible playbook

- Create a group named [webservers] inside the inventory and put servera and serverb inside it

```bash
$ cat > inventory
…
[loadbalancer]
servera

[webservers]
serverb
serverc
```

```bash
$ ansible servera -m ping
```


- Make sure an ~/.ansible-navigator.yml file in the student user home directory

```bash
$ cat ~/.ansible-navigator.yml
```
```yaml
---
ansible-navigator:
  execution-environment:
    image: utility.lab.example.com/ee-supported-rhel8
    pull:
      policy: missing

  playbook-artifact:
	enable: false
  
  #mode: stdout
```


- Create an index.html.j2 Jinja2 template file

```bash
$ cat > ~/control-flow/index.html.j2
```
```bash
<h2>Welcome to the corporate intranet portal. This is the landing page of {{ inventory_hostname }}</h2>
```

- Create a playbook that install an HAProxy on servera and httpd on serverb and servers

```bash
$ cat > ~/control-flow/playbook-lb-web.yml
```
```yaml
---
# HAProxy config
- name: configure prod env
  hosts: all
  # ignore_errors: yes
  vars:
     dummy_backends: 'server  app[0-9] .* check'
     backends: 
     - server serverb 172.25.250.11:80 check
     - server serverc 172.25.250.12:80 check
     haproxy_port: "5000/tcp"

  tasks:
  - name: configure HAProxy
    block:
    - name: Install HAProxy
      yum:
        name: haproxy
        state: present

    - name: Delete dummy servers from HAProxy config
      lineinfile:
        path: /etc/haproxy/haproxy.cfg
        regexp: "{{ dummy_backends }}"
        state: absent 

    - name: Insert web backend
      lineinfile:
        path: /etc/haproxy/haproxy.cfg
        state: present
        line: "{{ item }}" 
      loop: "{{ backends }}"
      notify:
      - Restart HAProxy

    - name: Start haproxy
      service:
        name: haproxy
        state: started
        enabled: true

    - name: Allow port 5000
      firewalld:
        port: "{{ haproxy_port }}"
        state: enabled
        immediate: true
        permanent: true
    
    when: inventory_hostname == "servera"

# Web servers config

  - name: Web tier config
    block:
    - name: Install httpd
      yum:
        name: httpd
        state: present

    - name: Config web page
      template:
        src: ./index.html.j2
        dest: /var/www/html/index.html

    - name: Start httpd service
      service:
        name: httpd
        state: started
        enabled: true

    - name: Allow http service
      firewalld:
        service: http
        state: enabled
        immediate: true
        permanent: true
    
    when: '"webservers" in group_names'


  handlers:
  - name: Restart HAProxy
    service:
      name: haproxy
      state: restarted
```