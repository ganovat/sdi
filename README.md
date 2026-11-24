# Secure digital infrastructure

This is a technical documentation of an the installation and configuration of a server using Linux command line on an Ubuntu server hosted
on a VM

# Table of contents 


# Configuration

## Acounts:
The server consists of 3 accounts audit marketing and design

Marketing is only allowed to up[load file to its home directory and the website using SFTP, it is part
of the group webdata

Design is allowed shell access for server side commands, and to upload files to their home directory, but not the website

Audit has read-only SFTP access to marketing and design's accounts

## Groups

Webdata-

Sftpmember-

readHome-

Directories:


# Maintenance tasks
