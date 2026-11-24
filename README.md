# Secure Digital Infrastructure 661982

This is a technical documentation of an the installation and configuration of a server using Linux command line on an Ubuntu server hosted
on a VM

# Table of contents 


# Configuration

## Accounts:
The server consists of 4 accounts audit marketing and design

Marketing is only allowed to upload file to its home directory and the website using SFTP, it is part
of the group sftpMember and its own group. It has read and write access to /srv/www as it owns it

Design is allowed shell access for server side commands, and to upload files to their home directory, but not the website. Design belongs to its own group

Audit has read-only SFTP access to marketing and design's accounts, the account is a part of the groups webdata, sftpMember, readHome and
its own group

www-data is a system user which is created by nginx which is part of the group webdata

## Groups
Groups are in place to assist with access control

webdata- Is in place to allow the audit account and nginx to read the contents of /srv/www. Essentially it acts to control the read access of /srv/www

sftpMember- Has accounts that are only granted SFTP and no shell access

readHome- Is a dedicated group for audit to use to get read access to marketing and design's files

Directory permissions:
For the directories /srv/www, /home/design, and /home/marketing: the chmod 2750 command was used here to ensure that every new file created in these directories inherit the directory's group, this for example will allow the audit account as well as nginx to read anything uploaded, as well


For /home/audit the chmod  750 command was used here to make it so that only the owner(audit) and the users in the same group access it


# Maintenance tasks

