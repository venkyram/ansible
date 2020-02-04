### Ad-hoc command to install Python on the host ansible2.example.com

```bash
ansible ansible2.example.com -m raw -a "yum install -y python3 && alternatives --set python /usr/bin/python3"
```

### Playbook for the httpd, firewall enablement and the welcome file

```bash
[ansible@control]$ cat day1-homework.yml
---
- name: httpd deploy
  hosts: ansible2.example.com
  tasks:
    - name: httpd and firewalld install step
      yum:
        name:
          - httpd
          - firewalld
        state: latest
    - name: Kickoff httpd services
      service:
        name: httpd
        enabled: true
        state: started
    - name: Kickoff firewalld services
      service:
        name: firewalld
        enabled: true
        state: started
    - name: open firewall for httpd
      firewalld:
        service: http
        permanent: yes
        state: enabled
        immediate: true
    - name: setup welcome page
      copy:
        content: “Welcome to my web server Day 1 Homework”
        dest: /var/www/html/index.html


[ansible@control]$ ansible-playbook day1-homework.yml 

PLAY [httpd deploy] *****************************************************************************************

TASK [Gathering Facts] **************************************************************************************
ok: [ansible2.example.com]

TASK [httpd and firewalld install step] *********************************************************************
changed: [ansible2.example.com]

TASK [Kickoff httpd services] *******************************************************************************
changed: [ansible2.example.com]

TASK [Kickoff firewalld services] ***************************************************************************
changed: [ansible2.example.com]

TASK [open firewall for httpd] ******************************************************************************
changed: [ansible2.example.com]

TASK [setup welcome page] ***********************************************************************************
changed: [ansible2.example.com]

PLAY RECAP **************************************************************************************************
ansible2.example.com       : ok=6    changed=5    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

### Playbook to remove the above deployment

```bash
[ansible@control]$ cat day1-homework-remove.yml 
---
- name: httpd un-deploy
  hosts: ansible2.example.com
  tasks:
    - name: close firewall for httpd
      firewalld:
        service: http
        permanent: true
        state: disabled
        immediate: yes
    - name: Stop httpd services
      service:
        name: httpd
        enabled: true
        state: stopped
    - name: Stop firewalld services
      service:
        name: firewalld
        enabled: true
        state: stopped
    - name: Remove welcome page
      file:
        dest: /var/www/html/index.html
        state: absent
    - name: httpd and firewalld uninstall step
      yum:
        name:
          - httpd
          - firewalld
        state: removed

[ansible@control]$ ansible-playbook day1-homework-remove.yml 

PLAY [httpd un-deploy] **************************************************************************************

TASK [Gathering Facts] **************************************************************************************
ok: [ansible2.example.com]

TASK [close firewall for httpd] *****************************************************************************
changed: [ansible2.example.com]

TASK [Stop httpd services] **********************************************************************************
changed: [ansible2.example.com]

TASK [Stop firewalld services] ******************************************************************************
changed: [ansible2.example.com]

TASK [Remove welcome page] **********************************************************************************
changed: [ansible2.example.com]

TASK [httpd and firewalld uninstall step] *******************************************************************
changed: [ansible2.example.com]

PLAY RECAP **************************************************************************************************
ansible2.example.com       : ok=6    changed=5    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
