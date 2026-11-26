# SDI 661982

## Student: 908136
Documentation of installation and configuration of a server hosted on a VM


# Configuration

- web server root directory: `/srv/www`
## Accounts:
- `sysadmin` account and home deleted, and `ubuntu` password changed
  
- `nologin` for marketing and audit. bash for `design`. SSH key login only without passwords for all 3. No guessable passwords
 
- `marketing`: SFTP read/write to its home directory and `/srv/www`. Allows site publishing

- `design`: shell access and SFTP to home directory, but not the website. Directories `project_rocket/cad/render/` and `project_cheese/research/tests/` created in root.

- `audit`: read-only SFTP access to marketing and design homes and website.
  
- `www-data`: system user created by Nginx.


## Groups
- `webdata` (`audit`, `www-data` ): reads `/srv/www`

- `sftpMember` (`audit` and `marketing`): Forced into SFTP-only

- `readHome` (`audit`)- group owner of `marketing` and `design` home directories so it can read them. Enforces least privilege principle and allows scalability without changing home ownership

## Directories & permissions:
- `/srv/www`, `/home/design`, and `/home/marketing`: `chmod 2750` used. The Setgid ensures every new file inherits the directory's group allowing audit and Nginx to read files.

- `/home/audit`: chmod 750 used, only (`audit`) and the users in the same group access it

## Firewall
- UFW is enabled and only 22/tcp and 80/tcp are allowed through the firewall (VM security measure)

## SFTP
- In SSH configuration file `Match Group sftpMember` applies `ForceCommand internal-sftp`. Users in this group have no shell access.


## Web server
- Nginx listens on port 80
- root directory set to `/srv/www`
- The Nginx configuration file at `/etc/nginx/sites-available/staticSite` and it is symlinked into `/etc/nginx/sites-enabled/staticSite`
- `sites-available` and Symlinks used instead of writing to `sites-enabled` as it allows for easy disabling and re-enabling of a site without deletion
- `/srv/www/student/index.txt` contains 908136
- `default_server` is the static site, the IP and `stu-908136-vm1.net.dcs.hull.ac.uk` site serve it

## Docker
- Docker installed from apt repository
- SDI docker repository cloned, built and named `sdi-img`
- Container sdi-container runs on a user defined bridge sdi-network `172.24.0.0/24` at a fixed IP `172.24.0.8` with `restart policy --restart unless-stopped` (automatically starts container). Fixed IP lets Nginx know where to forwards a request every time.
- Ports not published (`-p 3000:3000`) so UFW cannot be bypassed. 
- Nginx proxies `docker.stu-908136-vm1.net.dcs.hull.ac.uk` to `172.24.0.8:3000`

  
# Maintenance tasks
- Updates: backup `/etc/nginx`, `/etc/ssh/sshd_config` and `/srv/www/` incase of configuration conflicts. Run `sudo apt update`, then `sudo apt upgrade` monthly for security
- Disk space: use `df -h`, and `sudo docker image prune` to remove dangling images
- Logs: use `journalctl -u nginx`, look into `/var/log/nginx` `/var/log/auth.log`, and use `sudo docker logs sdi-container`
- Before restarting a service run `sudo nginx -t` and `sudo sshd -t` , open a second SSH session to make potential fixes
- Update application: 1. `git pull`, 2. `sudo docker build -t sdi-img .` , 3. `sudo docker rm -f sdi-container`. Then run it using the same restart policy, fixed IP, and network
- New user: use useradd. Create `~/.ssh` (chmod 700) and `~/.ssh/authorized_keys` (chmod 600), , generate keys and paste public key into authorized_keys, private keys in local machine and add to group.
- Status check: `sudo ufw status` must always show Status: active, `systemctl status nginx docker ssh` must show all 3 services as running


# AI use
AI was used to explain error messages, configuration was done by me
