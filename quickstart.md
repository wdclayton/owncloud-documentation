# Quickstart Guide to the Installation and Configuration of ownCloud 10 on RHEL 7

This document is specific to installing ownCloud 10 on RHEL 7. For instructions using other Linux distributions please see your vendor's documentation or visit the [ownCloud vendor-neutral documentation](https://doc.owncloud.com/server/admin_manual/installation/manual_installation.html) for a detailed walk-through of this entire objective and more.

## Prerequisites

For our purposes we will be utilizing rpm's built-in package dependency resolution. See the full list of [ownCloud's prerequisite's here](https://doc.owncloud.com/server/admin_manual/installation/manual_installation.html#prerequisites).

### Enable the RHEL7 software repository

    subscription-manager repos --enable rhel-server-rhscl-7-eus-rpms

### Enable EPEL for dependencies

    rpm -ivh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
    rpm -ivh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm

### Install Apache and PHP

    yum install -y httpd php56w php56w-gd php56w-json php56w-pdo php56w-curl php56w-intl php56w-mcrypt php56w-zip php56w-xml php56w-mbstring php56w-mysqlnd

### Install MariaDB

    yum -y install wget mariadb-server mariadb

### Obtain the ownCloud RPM signing key

    rpm --import https://download.owncloud.org/download/repositories/production/RHEL_7/repodata/repomd.xml.key

### Add the Owncloud RPM repository

    cd /etc/yum.repos.d/
    wget http://download.owncloud.org/download/repositories/production/RHEL_7/ce:stable.repo

### Install the ownCloud package

    yum -y install owncloud-files

### Change the default port for Apache

Using the editor of your choice, edit /etc/apache/sites-enabled/000-default.conf to go from this:

    <VirtualHost *:80>

To this:

    <VirtualHost *:8080>

### Save the file and restart Apache

    systemctl restart httpd

### Start MariaDB:

    systemctl start mariadb

## MariaDB database configuration

### Create the ownCloud database

__Note:__ Be sure to run the mysql_secure_installation to secure MariaDB.

### Log in to MySQL server:

    mysql -u root -p

### Create a database named “clouddb”

    create database clouddb;

### Allow “clouddbuser” to access the “clouddb” database on localhost with a strong password of your choice.

    grant all on clouddb.* to 'clouddbuser'@'localhost' identified by '~>$H4rd2Cr4Ck!~>';

### Flush the privileges and exit from the MySQL shell

    FLUSH PRIVILEGES;
    exit

### Configure SELinux permissions

    semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/owncloud/data'
    restorecon '/var/www/html/owncloud/data'
    semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/owncloud/config'
    restorecon '/var/www/html/owncloud/config'
    semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/owncloud/apps'
    restorecon '/var/www/html/owncloud/apps'

### Configure the IPTables firewall

    iptables -A INPUT -p tcp -m tcp --dport 8080 -j ACCEPT
    service iptables save

## Post-Install ownCloud configuration

These are the basic steps in performing the post-installation configuration of your new ownCloud instance. For a more detailed walk-through and full explanation, see [ownCloud's description of this process](https://doc.owncloud.com/server/admin_manual/installation/installation_wizard.html#introduction). In a web browser navigate to your ownCloud instance with the following URL structure:

    https://your.owncloud.instance:8080/owncloud

Enter the necessary details to complete the server configuration as shown below:

![The ownCloud web configuration page](https://doc.owncloud.com/server/admin_manual/_images/installation/install-wizard-a.png)

_The ownCloud web configuration page._

Enter the MariaDB database details as shown below:

__Note:__ Be sure to select database type __MySQL/MariaDB__.

![The ownCloud database configuration page](https://doc.owncloud.com/server/admin_manual/_images/installation/install-wizard-a1.png)

_The ownCloud database configuration page._

This completes the post-installation tasks. You may now log into the ownCloud instance with your admin user name and password.

![The ownCloud home page with Splash Screen Popup](https://www.itzgeek.com/wp-content/uploads/2016/11/Install-ownCloud-10-on-Ubuntu-16.04-ownCloud-Apps-1024x621.png)

_Congratulations!_

## Manage users

User management is very intuitive. Simply navigate to the Users tab and fill out the form as shown below. For a more detailed explanation refer to [ownCloud's User Management documentation](https://doc.owncloud.org/server/9.1/admin_manual/configuration_user/user_configuration.html).

![Creating ownCloud Users](https://doc.owncloud.org/server/9.1/admin_manual/_images/users-create.png)

_Creating ownCloud users._

## Connecting client software to ownCloud

This section briefly describes obtaining the ownCloud desktop and mobile clients to access an ownCloud instance.

For a more detailed explanation see [ownCloud's client configuration documentation](https://doc.owncloud.com/desktop/).

### ownCloud desktop client

Download and run the client for your platform from the [ownCloud client download site](https://owncloud.com/desktop-app/).

Provide the necessary details for connecting to your ownCloud instance during the installation process.

Detailed information for the client can be found at the [ownCloud documentation page for the desktop client](https://doc.owncloud.com/desktop/installing.html).

### ownCloud mobile client

#### Android

The ownCloud Android app is available on the [Google Play Store](https://play.google.com/store/apps/details?id=com.owncloud.android).

Detailed information for the Android app is available from the [ownCloud documentation for the Android app](https://doc.owncloud.com/android/).

#### Apple iOS

The ownCloud iOS app is available on the [iOS App Store](https://apps.apple.com/us/app/owncloud-file-sync-and-share/id1359583808).

Detailed information for the iOS app is available from the [ownCloud documentation for the iOS app](https://doc.owncloud.com/ios/).
