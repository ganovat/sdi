# Secure Digital Infrastructure 661982

This is a technical documentation of an the installation and configuration of a server using Linux command line on an Ubuntu server hosted
on a VM


# Configuration

## Accounts:

- nologin was used for marketing and design hence they require SSH key login only without passwords
 
- marketing  is only allowed to upload file to its home directory and the website using SFTP, it is part
of the group sftpMember and its own group. It has read and write access to /srv/www as it owns it.

- design is allowed shell access for server side commands, and to upload files to their home directory, but not the website. Design belongs to its own group

-audit has read-only SFTP access to marketing and design's accounts, the account is a part of the groups webdata, sftpMember, readHome and
its own group

-www-data is a system user which is created by nginx which is part of the group webdata


## Groups
Groups are in place to assist with access control

-nwebdata- allows the audit account and nginx to read the contents of /srv/www. control the read access of /srv/www

- sftpMember- Has accounts that are granted SFTP-only and no shell access

- readHome- Dedicated group for audit to use to get read access to marketing and design's files as the home directories of marketing and design are group owned by it

## Directory permissions:
- For the directories /srv/www, /home/design, and /home/marketing: the chmod 2750 command was used. The Setgid ensures every new file created in these directories inherit the directory's group.
- /srv/www is owned by marketing and is in the webdata group
For /home/audit the chmod  750 command was used here to make it so that only the owner(audit) and the users in the same group access it

## Firewall
- UFW is enabled and only 22/tcp and 80/tcp are allowed through the firewall

## SFTP
-In the SSH configuration file Match Group sftpMember directive used to enforce a use of SFTP instead of shell access
- design is the only account that kept shell access

## Web server
- Nginx is on port 80
- root directory is set to /srv/www
- The Nginx configuration file is located at /etc/nginx/sites-available/staticSite and it is symlinked into /etc/nginx/sites-enabled/staticSite
- /srv/www/student/index.txt contain the student number 908136
- default_server is the static site, the IP and stu.908136-vm1.net.dcs.hull.ac.uk site serve it

## Docker
- Docker was installed using apt repository and can be updated with sudo apt update 
- SDI docker repository was cloned and built and named sdi-img
- The container sdi-container runs on a user defined bridge called sdi-network 172.24.0.0/24 at a fixed IP 172.24.0.8 with restart policy --restart unless-stopped
- The ports were not published so UFW cannot be bypassed ebsuring security
- Nginx proxies docker.stu-908135-vm1.net.dcs.hull.ac.uk to 172.24.0.8:3000
  
# Maintenance tasks
- sudo apt update must be done to prepare for an update, followed by sudo apt upgrade to install available upgrades
- For disk space management make use of df -h, and sudo docker image prune to remove dangling images
- For logs: use journalctl -u nginx, look into /var/log/nginx /var/log/auth.log, and use sudo docker logs sdi-container
- Before restarting a service test the configuration file with sudo nginx -t and sudo sshdm Keep a second SSH session open to make potential fixes
- To update the application when the repo changes: 1. git pull, 2. sudo docker build -t sdi-img, 3. sudo docker rm -f sdi-container. Then run it using the same policy and fixed IP
- For a new user: use useradd. create /.ssh/authorized_keys, use either chmod 600/700, generate a key and paste contents into authorized_keys
- Status check: sudo ufw status must always show Status: active, systemctl status nginx docker ssh must show all 3 services as running


# AI use
AI was used to explain error messages 
