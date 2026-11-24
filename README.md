# Secure Digital Infrastructure 661982

This is a technical documentation of an the installation and configuration of a server using Linux command line on an Ubuntu server hosted
on a VM

# Table of contents 

# Configuration

## Accounts:

-The server consists of 4 accounts audit, marketing, www-data, and design

- nologin was used for marketing and design hence they require SSH key login only without passwords

- 
Marketing is only allowed to upload file to its home directory and the website using SFTP, it is part
of the group sftpMember and its own group. It has read and write access to /srv/www as it owns it. Requires SSH key login only without passwords

Design is allowed shell access for server side commands, and to upload files to their home directory, but not the website. Design belongs to its own group

Audit has read-only SFTP access to marketing and design's accounts, the account is a part of the groups webdata, sftpMember, readHome and
its own group

www-data is a system user which is created by nginx which is part of the group webdata


## Groups
Groups are in place to assist with access control

webdata- Is in place to allow the audit account and nginx to read the contents of /srv/www. Essentially it acts to control the read access of /srv/www

sftpMember- Has accounts that are granted SFTP-only and no shell access

readHome- Is a dedicated group for audit to use to get read access to marketing and design's files

## Directory permissions:
- For the directories /srv/www, /home/design, and /home/marketing: the chmod 2750 command was used here. The Setgid ensures that every new file created in these directories inherit the directory's group, this for example will allow the audit account as well as nginx to read anything uploaded, as well
-
- /srv/www is owned by marketing and is in the webdata group
- the home directories of marketing and deign are group owned by 
For /home/audit the chmod  750 command was used here to make it so that only the owner(audit) and the users in the same group access it

## SFTP
-In the SSH configuration file Match Group sftpMember directive used to enforce a use of SFTP instead of shell access
- design is the only account that kept shell access

## Web server
- Nginx is on port 80
- root directory is set to /srv/www
- The Nginx configuration file is located at /etc/nginx/sites-available/staticSite and it is symlinked into /etc/nginx/sites-enabled/staticSite
- /srv/www/student/index.txt contain the student number 908136
- default_server is the static site, the IP and stu.908136-vm1.net.dcs.hull.ac.uk site serve it
# Maintenance tasks

