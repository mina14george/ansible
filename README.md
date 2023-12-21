```
# Ansible Playbook: Enable Intranet Services

## Objective
This Ansible playbook automates the setup of intranet services on the server `servera.lab.example.com`. It ensures the installation and configuration of `httpd`, sets up a basic HTML page, enables and configures firewalld, and verifies the setup by testing connectivity to the intranet web server.

## Playbook Structure
The playbook is divided into two main sections:

### Section 1: Enable Intranet Services
This section configures the intranet services on the designated server.

```yaml
- name: Enable intranet services
  hosts: servera.lab.example.com
  become: true
  tasks:
    - name: Latest version of httpd and firewalld installed
      ansible.builtin.dnf:
        name:
          - httpd
          - firewalld
        state: latest

    - name: Test html page is installed
      ansible.builtin.copy:
        content: "Welcome to the example.com intranet!\n"
        dest: /var/www/html/index.html

    - name: Firewall enabled and running
      ansible.builtin.service:
        name: firewalld
        enabled: true
        state: started

    - name: Firewall permits access to httpd service
      ansible.posix.firewalld:
        service: http
        permanent: true
        state: enabled
        immediate: yes

    - name: Web server enabled and running
      ansible.builtin.service:
        name: httpd
        enabled: true
        state: started
```

### Section 2: Test Intranet Web Server
This section validates the intranet web server setup by performing a connection test from the local machine.

```yaml
- name: Test intranet web server
  hosts: localhost
  become: false
  tasks:
    - name: Connect to intranet web server
      ansible.builtin.uri:
        url: http://servera.lab.example.com
        return_content: yes
        status_code: 200
```

## Usage
To execute this playbook:

1. Save the YAML content into a file, e.g., `enable_intranet.yml`.
2. Run the playbook using the `ansible-playbook` command:
   
    ```bash
    ansible-playbook enable_intranet.yml
    ```
Ensure you have proper Ansible configurations and access to the specified hosts before running the playbook.
```
