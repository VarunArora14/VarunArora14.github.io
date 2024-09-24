---
title: "Apache server concepts"
date: 2024-07-20T22:26:12+05:30
draft: false
tags: ["Apache", "server"]
---

### httpd.conf vs .htacess

httpd.conf and .htaccess are both configuration files used in Apache web server, but they have different scopes and purposes.

**1. httpd.conf**

**httpd.conf** is the main configuration file for Apache, which contains global configuration settings that apply to the entire server. It is usually located in the Apache installation directory or in the **/etc/httpd/** (or /etc/apache2/ on some systems) directory, and it requires administrative privileges to edit.

The **httpd.conf** file is used to configure various aspects of the Apache server, including server-wide settings such as server name, ports, server modules, virtual hosts, and security settings. Changes made to httpd.conf require a server restart to take effect.

**.htaccess**:
On the other hand, **.htaccess** is a **per-directory configuration file** that allows you to **override** or add to the global configuration settings of Apache on a per-directory basis. It is **usually placed in the document root directory** of a web server, and it can be edited by webmasters or site administrators without requiring administrative privileges.

The **.htaccess** file is used to configure directory-specific settings such as URL rewriting, access control, authentication, and other web server directives. Changes made to .htaccess take effect immediately without requiring a server restart.

One key difference between **httpd.conf** and **.htaccess** is the scope of their configuration settings. httpd.conf applies **globally** to the entire server, while .htaccess applies only to the **directory** in which it is located and its **subdirectories**. This allows for more fine-grained configuration control at the directory level using .htaccess files.

It's worth noting that the use of .htaccess files can **impact server performance**, as **Apache needs to check for .htaccess files on each request**. Therefore, it is generally recommended to use **httpd.conf** for **server-wide** settings whenever possible, and use **.htaccess** for **directory-specific settings** only when necessary.

---

### VirtualHosts

In Apache, virtual hosts are used to **host multiple websites on a single server**, allowing **each website to have its own configuration settings, files, and behavior**. **Virtual hosts** are created by defining separate configurations for each website, typically using separate configuration files, and specifying different settings such as the DocumentRoot, ServerName, and other directives for each virtual host.
Here's an example of how to define virtual hosts in Apache using separate configuration files:

1. Create a configuration file for each virtual host in the "**sites-available**" directory. For example, create a file called "**example1.com.conf**" with the following contents:

```
<VirtualHost *:80>
    ServerName example1.com
    DocumentRoot /var/www/example1.com
    # Other configuration settings specific to example1.com
</VirtualHost>
```

2. Create another file named **example2.com.conf** with following contents:

```
<VirtualHost *:80>
    ServerName example2.com
    DocumentRoot /var/www/example2.com
    # Other configuration settings specific to example2.com
</VirtualHost>
```

3. Create symbolic links to these configuration files in the "**sites-enabled**" directory. On Ubuntu/Debian, you can use the "**a2ensite**" command to enable a virtual host and create a symbolic link:

```
sudo a2ensite example1.com.conf
sudo a2ensite example2.com.conf
```

---

### Important Directives in Apache

**Directives** are set of rules which define how your server should run, the number of clients that can access your server, etc. which can be configured in **httpd.conf**.

Some important directives are -

- **DocumentRoot** - Defines the **directory server should look for files**. This is the **root directory of the website**.
- **ServerName** - It is the **name of of virtual host** to identify site served by Apache. It can be domain name like `ServerName www.example.com:80` or if not mentioned, it is the same IP address.
- **IfModule** - It allows **conditional configuration** based on **availability** of specific module. It wraps configuration directives that are only intended to be processed if a specific module is **loaded** and **available** in the Apache server.

**This allows for safe configuration that doesn't cause errors or conflicts when a required module is not available. It also provides flexibility to enable or disable specific configuration settings based on the presence or absence of certain modules, making it useful in complex Apache configurations where different modules may be installed or enabled on different servers or virtual hosts.**
Syntax:

```
<IfModule module-name>
    # Configuration directives specific to the module
</IfModule>
```

- **Directory** - Controls **configuration settings** for **specific directory** or directory hierarchy within **DocumentRoot**. It can be used to **set permissions, authentication, and other security-related settings**.
- **Files** - directive is used to **set conditional configuration settings** for **filetypes** such as below (prevent .htaccess and .htpasswd files from being viewed by Web clients )

```
<Files ".ht*">
    Require all denied
</Files>
```

---

### sites-enabled and sites-available

In Apache, the "**sites-available**" and "**sites-enabled**" directories are used for organizing virtual host configurations, which are used to **host multiple websites on a single Apache server**. Here's what each directory does:

1. **sites-available**: This directory contains configuration files for **available virtual hosts**, which are websites that **can** be hosted on the Apache server. **Each configuration file represents a virtual host and contains directives that define how that virtual host should behave, such as the DocumentRoot, ServerName, and other settings**. These configuration files are created manually or through automated tools and are typically stored in the /etc/apache2/sites-available directory (on Ubuntu/Debian) or /etc/httpd/sites-available directory (on CentOS/RHEL).

2. **sites-enabled**: This directory contains symbolic links to the configuration files of **enabled virtual hosts**. When a virtual host is enabled, the symbolic link to its configuration file is created in the "sites-enabled" directory. This is typically done using the "**a2ensite**" command on Ubuntu/Debian or by manually creating symbolic links on CentOS/RHEL. Apache reads the virtual host configurations from the "**sites-enabled**" directory during runtime, and any changes made to the symbolic links or the actual configuration files will take effect after an Apache restart or reload. **a2dissite** is used to make config file **example1.conf** go from **sites-enabled to sites-available**

The separation of "**sites-available**" and "**sites-enabled**" directories provides a way to **easily enable or disable virtual hosts without modifying the actual configuration files**. It allows for **better organization and management of virtual host configurations**, especially when hosting multiple websites on a single Apache server. By creating and managing virtual host configurations in the "sites-available" directory and enabling or disabling them using symbolic links in the "sites-enabled" directory, you can **easily control which virtual hosts are active at any given time on your Apache server**. We can automate controlling the virtual hosts being active at a given time.

### Conf-available vs Conf-enabled

- **conf-available** - This directory contains configuration files that are **available** for Apache to use but are **not enabled by default**. Each configuration file in "conf-available" typically represents a **module or feature configuration, such as enabling or disabling a specific module, setting global server settings, or defining custom directives**. These configuration files are created manually or through automated tools and are typically stored in the /etc/apache2/conf-available directory (on Ubuntu/Debian) or /etc/httpd/conf-available directory (on CentOS/RHEL).
- **conf-enabled**: This directory contains symbolic links to the configuration files that are enabled and active in the Apache server. When a configuration file is enabled, a symbolic link to its actual configuration file in the "conf-available" directory is created in the "conf-enabled" directory. This is typically done using the "a2enconf" command on Ubuntu/Debian or by manually creating symbolic links on CentOS/RHEL. Apache reads the enabled configuration files from the "conf-enabled" directory during runtime, and any changes made to the symbolic links or the actual configuration files will take effect after an Apache restart or reload.

---

### filters and handlers

They **process the content of HTTP requests or responses before they are sent or received by the web server**. Filters allow for manipulation or transformation of the content, such as **modifying headers, transforming data, or adding/removing content**, as it passes through the web server.

Any processing such as compression, rate limiting, allowing specific file types before the request is been served to server is done by filters.

Filters are defined using **Apache directives in the server or virtual host configuration files**, and they can be used to modify the behavior of the server, customize content for different clients, apply security measures, and more. Filters can be chained together in a specific order to process requests or responses in a sequential manner. The order of filters can be controlled using the FilterChain directive.

Here's an example of how to configure an output filter in Apache to enable gzip compression on response bodies using mod_deflate:

```
# Enable mod_deflate
LoadModule deflate_module modules/mod_deflate.so

# Define the output filter
<IfModule mod_deflate.c>
    AddOutputFilterByType DEFLATE text/html text/plain text/xml
</IfModule>
```

In this example, the "**AddOutputFilterByType**" directive is used to enable **gzip compression** for specific MIME types (text/html, text/plain, text/xml). The mod_deflate module is loaded using the "LoadModule" directive, and the filter is defined within an IfModule block to ensure it is only applied if the mod_deflate module is available.

## Handler

A "handler" is an internal Apache representation of the **action to be performed when a file is called**. Generally, files have implicit handlers, based on the file type. Normally, all files are simply served by the server, but certain file types are "handled" separately.

```conf
# Files of a particular file extension
AddHandler my-file-type .xyz
Action my-file-type "/cgi-bin/program.cgi"
```

In this example, requests for files with a file extension of .xyz are handled by the specified cgi script /cgi-bin/program.cgi.

```
# Map .php files to the PHP handler
AddHandler php5-script .php

# Map .php7 files to the PHP handler
AddHandler php7-script .php7
```

```
# Map .php files to the PHP handler
AddHandler php5-script .php

# Map .php7 files to the PHP handler
AddHandler php7-script .php7
```

In this example, the "**php5-script**" and "**php7-script**" handlers are used to serve PHP files with the extensions ".php" and ".php7", respectively. **When a request is made for a file with these extensions, the web server will pass the request to the PHP handler, which will process the PHP code and generate the dynamic HTML output to be sent to the client**.

---

### .htaccess deepdive

The **.htaccess** file is a special configuration file used in Apache web servers to **define directory-specific configuration settings** that affect the **behavior** of that **directory and its subdirectories**. The name ".htaccess" stands for "hypertext access" and the file is typically written in plain text.

The **.htaccess** file is placed in the **directory for which the configuration settings apply**, and it can contain various Apache directives that control various aspects of web server behavior, such as **URL rewriting, authentication, caching, and custom error pages**, among others.

The use cases are -

- **URL Rewriting** - **.htaccess** files can define rules to rewrite URLs, allowing for custom URL mappings or redirects. This can be useful for creating search engine-friendly URLs or redirecting old URLs to new ones.
- **Authentication** - Specify authentication settings, such as **password protection, for specific directories**, allowing for directory-level access control. This can be useful for restricting access to certain parts of a website.
- **Custom Error Pages** - It can define custom error pages such as 404 or 500 for specific directories, providing a personalized user experience when errors occur.
- **Caching** - .htaccess files can be used to configure caching settings or enable compression for certain file types, improving website performance

```
# Enable Gzip compression for certain file types
<IfModule mod_deflate.c>
    AddOutputFilterByType DEFLATE text/html text/plain text/xml
</IfModule>

# Enable caching for certain file types
<IfModule mod_expires.c>
    ExpiresActive On
    ExpiresByType image/jpeg "access plus 1 year"
    ExpiresByType text/css "access plus 1 month"
</IfModule>
```

- **MIME types**: .htaccess files can be used to define custom MIME types for file types that are not recognized by the server, allowing for proper handling of file downloads or other content types.

For enabling **.htaccess**, enable .**htaccess** support in Apache: In the Apache server configuration (httpd.conf) file, you need to make sure that the **AllowOverride** directive is set to **All** for the directory where you want to use .htaccess.

```
<Directory /var/www/html>
    AllowOverride All
</Directory>
```
