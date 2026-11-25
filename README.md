# Secure Digital Infrastructure 661982

Documentation of installation and configuration of a server hosted on a VM


# Configuration

## Accounts:
- sysadmin account deleted
  
- nologin was used for marketing and audit, and bash for design they require SSH key login only without passwords
 
- marketing can only upload files to its home directory and the website using SFTP. Has read and write access to /srv/www.

- design is allowed shell access, and to upload files to their home directory, but not the website.

- audit has read-only SFTP access to marketing and design, the account is a part of the groups webdata, sftpMember, readHome and
its own group

- www-data is a system user which is created by nginx which is part of the group webdata


## Groups
- webdata (members)- allows the audit account and nginx to read the contents of /srv/www. control the read access of /srv/www

- sftpMember (members)- Has accounts that are granted SFTP-only and no shell access

- readHome (members)- Dedicated group for audit to read marketing and design's files and directories. Created to enforce least privilege principle and allow scalibilty if another auditor needs to be added

## Directories & permissions:
- For the directories /srv/www, /home/design, and /home/marketing: the chmod 2750 command was used. The Setgid ensures every new file created in these directories inherit the directory's group allowing audit and Nginx to read files.
- Directories project_rocket/cad/render/ and project_cheese/research/tests/ created in root of design home directory
- /srv/www is owned by marketing 

- For /home/audit the chmod  750 command was used here to make it so that only the owner(audit) and the users in the same group access it

## Firewall
- UFW is enabled and only 22/tcp and 80/tcp are allowed through the firewall

## SFTP
- In the SSH configuration file Match Group sftpMember directive used to enforce a use of SFTP instead of shell access
- design is the only account that kept shell access

## Web server
- Nginx is on port 80
- root directory is set to /srv/www
- The Nginx configuration file is located at /etc/nginx/sites-available/staticSite and it is symlinked into /etc/nginx/sites-enabled/staticSite
- sites-available and Symlinks used instead of writing to sites-enabled as it makes maintainability easier
- /srv/www/student/index.txt contain the student number 908136
- default_server is the static site, the IP and stu-908136-vm1.net.dcs.hull.ac.uk site serve it

## Docker
- Docker installed using apt repository
- SDI docker repository cloned, built and named sdi-img
- The container sdi-container runs on a user defined bridge sdi-network 172.24.0.0/24 at a fixed IP 172.24.0.8 with restart policy --restart unless-stopped
- Ports not published (p 3000:3000) so UFW cannot be bypassed
- Nginx proxies docker.stu-908136-vm1.net.dcs.hull.ac.uk to 172.24.0.8:3000

  
# Maintenance tasks
- Run sudo apt update, followed by sudo apt upgrade regualry to ensure security
- For disk space management use df -h, and sudo docker image prune to remove dangling images
- For logs: use journalctl -u nginx, look into /var/log/nginx /var/log/auth.log, and use sudo docker logs sdi-container
- Before restarting a service test the configuration file with sudo nginx -t and sudo sshd -t Keep a second SSH session open to make potential fixes
- To update the application when the repo changes: 1. git pull, 2. sudo docker build -t sdi-img . , 3. sudo docker rm -f sdi-container. Then run it using the same policy and fixed IP
- For a new user: use useradd. create /.ssh/authorized_keys, use either chmod 600/700, generate a key and paste contents into authorized_keys
- Status check: sudo ufw status must always show Status: active, systemctl status nginx docker ssh must show all 3 services as running


# AI use
AI was used to explain error messages 
