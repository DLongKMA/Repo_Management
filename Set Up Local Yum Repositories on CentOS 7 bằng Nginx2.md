# Set Up Local Yum Repositories on CentOS 7 bằng Nginx

This article contains the following topics:
+ Step 1: Setup Nginx on the Repo Server
+ Step 2: Create Yum Local Repository 
+ Step 3: Create Cron Job to Synchronize and Create Repositories
+ Step 4: Setup Local Yum Repository on Client Machines

## 1. Preconditions: 

- Local repository storage requirements are based on the number of repositories you are enabling and syncing from. If you are enabling only the repositories mentioned below, 150GB are enough, otherwise- we recommend allocating up to 400 GB.
- Local repository hardware specifications is based on the number of endpoints connecting to the repo
- Each operating system should have its own repository server. (i.e Repo for each of the following - RHEL 7, RHEL 6, CentOS 7..)
- Local repository must have Internet connectivity

## Before we start:
- Please take under consideration the 'repo-sync' command, which might take 9-10 hours to complete.
- If you are familiar with the needed repositories in your organization, other than the suggested below, please configure them the exact same way. 

## 2. Step 1: Setup Nginx on the Repo Server

+ First start by installing Nginx HTTP server from the EPEL repository using the YUM package manager as follows.

  ```
  yum install epel-release
  yum install nginx 
  ```

+ Once you have installed Nginx web server, you can start it for the first time and enable it to start automatically at system boot.

  - CentOS 6 Command:

    ```
    service nginx start
    ```

  - CentOs 7 Commands:

    ```
    systemctl start nginx
    systemctl enable nginx
    ```

+ Next, you need to open port 80 and 443 to allow web traffic to Nginx service, update the system firewall rules to permit inbound packets on HTTP and HTTPS using the commands below

  - CentOS 6 Commands:
    ```
    sudo iptables -A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
    sudo /etc/init.d/iptables save
    iptables -A INPUT -p tcp -m state --state NEW -m tcp --dport 443 -j ACCEPT
    service iptables save
    service iptables restart
    ```
  - CentOs 7 Commands:
    ```
    firewall-cmd --zone=public --permanent --add-service=http
    firewall-cmd --zone=public --permanent --add-service=https
    firewall-cmd --reload
    ```
+ Confirm that your Nginx server is up and running, using the following URL; if you see the default Nginx web page, all is well. http://SERVER_DOMAIN_NAME_OR_IP 

## 3. Step 2: Create Yum Local Repository 

+ Install the required packages for creating, configuring and managing your local repository.
  
  ```
  yum install createrepo  yum-utils
  ```
+ Create the necessary directories (yum repositories) that will store packages and any related information.
  
  ```
  mkdir -p /var/www/html/repos/{base,centosplus,extras,updates} 
  ```
  ```
  mkdir -p /var/www/html/repos/base
  mkdir -p /var/www/html/repos/centosplus
  mkdir -p /var/www/html/repos/extras
  mkdir -p /var/www/html/repos/updates
  ```
  
+ Use the reposync tool to synchronize CentOS YUM repositories to the local directories as shown.
  
  ```
  reposync -g -l -d -m --repoid=base --newest-only --download-metadata --download_path=/var/www/html/repos/base
  reposync -g -l -d -m --repoid=centosplus --newest-only --download-metadata --download_path=/var/www/html/repos/centosplus
  reposync -g -l -d -m --repoid=extras --newest-only --download-metadata --download_path=/var/www/html/repos/extras
  reposync -g -l -d -m --repoid=updates --newest-only --download-metadata --download_path=/var/www/html/repos/updates
  ```
  
+ Create a new repodata for each repositories by running the following commands, where the flag -gis used to update the package group information using the specified .xml file.
  
  ```
  sudo createrepo /var/www/html
  ```
  
+ To enable viewing of repositories and packages in them, via a web browser, create a Nginx server block which points to the root of your repositories as shown.
  
  ```
  vim /etc/nginx/conf.d/repos.conf 
  ```
  
  Add the following configuration to file repos.conf:
  
  ```
  server {
   listen 80;
   server_name 10.168.6.60; #change test.lab to your real domain or IP address
   root /var/www/html/;
   location / {
   index index.php index.html index.htm;
   autoindex on; #enable listing of directory index
   }
  }
  ```
  
+ Then restart your Nginx server and view the repositories from a web browser using the following URL.
  
  ```
  http://10.168.6.60
  ```
  
## 4. Step 3: Create Cron Job to Synchronize and Create Repositories

Add a cron job that will automatically synchronize your local repos with the official CentOS repos to grab the updates and security patches.
  
```
vim /etc/cron.daily/update-localrepos
```
  
Add these commands in the script:
  
```
#!/bin/bash
##specify all local repositories in a single variable
LOCAL_REPOS=”base centosplus extras updates”
##a loop to update repos one at a time
for REPO in ${LOCAL_REPOS}; do
reposync -g -l -d -m --repoid=$REPO --newest-only --download-metadata --download_path=/var/www/html/repos/
createrepo -g comps.xml /var/www/html/repos/$REPO/ 
done
```
  
Save the script and close it and set the appropriate permissions on it.

```
chmod 755 /etc/cron.daily/update-localrepos
```

## 5. Step 4: Setup Local Yum Repository on Client Machines

On your CentOS client machines, add your local repos to the YUM configuration. (or run the provided script "create-repo-jetpatch-centos")

```
sudo vim /etc/yum.repos.d/remote.repo
```
```
[remote]
name=RHEL Apache
baseurl=http://10.168.6.60
enabled=1
gpgcheck=0
```

Save the file and start using your local YUM mirrors. Run the following command to view your local repos in the list of available YUM repos, on the client machines.

```
yum clean all
yum repolist all
```

