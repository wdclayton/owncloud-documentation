# Quickstart Guidce to the Installation and Configuration of ownCloud 10 on RHEL 7

This document is specific to installing ownCloud 10 on RHEL 7. For instructions using other Linux distributions please see your vendor's documentation or visit the [ownCloud vendor-neutral documenatation](https://doc.owncloud.com/server/admin_manual/installation/manual_installation.html) for a detailed walkthrough of this entire objective and more.

## Prerequisites

For our purposes we will be utilizing rpm's built-in package dependency resolution. See the full list of [ownCloud's prerequisite's here](https://doc.owncloud.com/server/admin_manual/installation/manual_installation.html#prerequisites).

### Enable the RHEL7 software repository:

    subscription-manager repos --enable rhel-server-rhscl-7-eus-rpms

### Enable EPEL for dependencies:

    rpm -ivh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
    rpm -ivh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm

### Install Apache and PHP:

    yum install -y httpd php56w php56w-gd php56w-json php56w-pdo php56w-curl php56w-intl php56w-mcrypt php56w-zip php56w-xml php56w-mbstring php56w-mysqlnd

### Install MariaDB:

    yum -y install wget mariadb-server mariadb

### Obtain the ownCloud RPM signing key:

    rpm --import https://download.owncloud.org/download/repositories/production/RHEL_7/repodata/repomd.xml.key

### Add the Owncloud repo:

    cd /etc/yum.repos.d/ 
    wget http://download.owncloud.org/download/repositories/production/RHEL_7/ce:stable.repo

### Install the ownCloud package itself:

    yum -y install owncloud-files

### Change the Default Port for Apache:

Using the editor of your choice, edit /etc/apache/sites-enabled/000-default.conf to go from this:

    <VirtualHost *:80>

To this:

    <VirtualHost *:8080>

### Save the file and restart Apache:

    systemctl restart httpd

### Start MariaDB:

    systemctl start mariadb

## MariaDB Database Configuration

### Create the ownCloud Database:

__Note:__ Be sure to run the mysql_secure_installation to secure MariaDB.

### Log in to MySQL server:

    mysql -u root -p

### Create a database named “clouddb”:

    create database clouddb;

### Allow “clouddbuser” to access the “clouddb” database on localhost with a strong password of your choice.

    grant all on clouddb.* to 'clouddbuser'@'localhost' identified by '~>$H4rd2Cr4Ck!~>';

### Flush the privileges and exit from the MySQL shell:

    FLUSH PRIVILEGES;
    exit

### Configure SELinux:

    semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/owncloud/data'
    restorecon '/var/www/html/owncloud/data'
    semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/owncloud/config'
    restorecon '/var/www/html/owncloud/config'
    semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/owncloud/apps'
    restorecon '/var/www/html/owncloud/apps'

### Firewall Configuration

#### IPTables:

    iptables -A INPUT -p tcp -m tcp --dport 8080 -j ACCEPT
    service iptables save

#### FirewallD:

    firewall-cmd --permanent --add-service=http
    firewall-cmd --permanent --add-service=https
    firewall-cmd --reload

## Post-Install ownCloud Configuration

These are the basic steps in performing the post-installation configuration of your new ownCloud instance. For a more detailed walkthrough and full explanation, see [ownCloud's description of this process](https://doc.owncloud.com/server/admin_manual/installation/installation_wizard.html#introduction). In a web browser navigate to your ownCloud instance with the following URL structure:

    https://your.owncloud.instance:8080/owncloud

Enter the necessary details to complete the server configuration as shown below:

![The ownCloud web configuration page](https://doc.owncloud.com/server/admin_manual/_images/installation/install-wizard-a.png)

_The ownCloud web configuration page_

Enter the MariaDB database details as shown below:

__Note:__ Be sure to select database type __MySQL/MariaDB__.

![The ownCloud database configuration page](https://doc.owncloud.com/server/admin_manual/_images/installation/install-wizard-a1.png)

_The ownCloud database configuration page_

This completes the post-installation tasks. You may now log into the ownCloud instance with your admin user name and password.

![The ownCloud home page with Splash Screen Popup](https://www.itzgeek.com/wp-content/uploads/2016/11/Install-ownCloud-10-on-Ubuntu-16.04-ownCloud-Apps-1024x621.png)

_Congratulations!_

## Creating Users

User management is very intuitive. Simply navigate to the Users tab and fill out the form as shown below. For a more detailed explanation refer to [ownCloud's User Management documentation](https://doc.owncloud.org/server/9.1/admin_manual/configuration_user/user_configuration.html).

![Creating ownCloud Users](https://doc.owncloud.org/server/9.1/admin_manual/_images/users-create.png)

_Creating ownCloud users_


