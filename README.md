#wordpress2fe

WordPress deployment automation with Docker, Ansible and 2 nginx frontends.

##Requirements

- Ansible 2.5 or greater;
- Docker 1.13.1;
- Docker SDK for Python;
- nginx 1.12.2 or greater(frontend);

##Tested with:

- Ansible 2.8;
- Docker 1.13.1;
- nginx 1.12.2 (frontend);
- CentOS 7 (1810).

## Examples

1) To run whole scenario (update "hosts" with desired hosts, "wordpressdeployment.yml" with new hosts):
```sh
ansible-playbook wordpressdeployment.yml --ask-pass --ask-become-pass
```

2) To include roles to your playbook:
```yaml
- name: Install wordpress, nginx, php-fpm, mariadb
  hosts: dockerhost
  remote_user: ansuser
  become: yes
  become_method: sudo
  roles:
    - wpdocker

- name: Add config to the frontend servers
  hosts: nginxfrontends
  remote_user: ansuser
  become: yes
  become_method: sudo
  vars:
    dockernginx: "{{ groups['dockerhost'][0] }}"
  roles:
    - nginxfe
```
dockernginx variable used to create template for nginx frontend at nginxfe role.

##Additional info:

Target host should be in known_hosts.
It is supposed Ansible is able to access all hosts using ansuser with sudo privileges.
It is supposed nginx has standard directory tree with "conf.d" directory. Debian-like style with "sites-available" and "sites-enabled" is not supported.
It is supposed example.com has DNS A-records with ip of nginx frontends.
All docker images are official and pulled from docker hub.
## Roles 

1) wpdocker
Creates:
- docker network;
- docker volumes;
- images of mariadb, wordpress with php-fpm, nginx;
- nginx config for dockerized nginx;
- /opt/nginx/conf.d at host in not exists.
Maps created network to all containers.
Mounts:
- created volumes containers (1 - wordpress and nginx, 2 - mariadb);
- nginx config from /opt/nginx/conf.d to /etc/nginx/conf.d in container.
Runs created containers.

2) nginxfe
Creates config for dockerizes application publishing.
Places config to frontend servers.
Restarts nginx service.


