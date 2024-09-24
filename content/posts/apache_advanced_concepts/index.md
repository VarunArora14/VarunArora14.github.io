---
title: "Apache server advanced topics"
date: 2024-08-08T12:26:12+05:30
draft: false
tags: ["Apache", "web server", "DocumentDB", "AWS"]
---

## MaxRequestWorkers

MaxRequestWorkers is a configuration directive in the Apache web server that specifies the **maximum number of simultaneous connections that the server can handle**. It determines the **maximum number of child processes or threads that can be spawned by the server to handle incoming requests**.

The MaxRequestWorkers directive is typically set in the Apache configuration file (**httpd.conf**) and determines the **maximum number of worker processes that Apache can create to serve client requests. Each worker process can handle one client request at a time. When the maximum number of workers is reached, additional requests will be queued, waiting for a worker process to become available**.

When a client sends a request to the Apache web server, the server creates a child process or thread to handle that request.
The MaxRequestWorkers directive specifies the **maximum number of these child processes or threads** that can be created at any given time. If the server reaches this limit, it will stop accepting new requests until some of the existing connections are closed.

The **MaxRequestWorkers** directive is important for ensuring that your Apache server can handle a large number of **concurrent connections** without becoming **overloaded** or **crashing**. The optimal value for this directive depends on factors such as the available system resources, the nature of the requests being handled, and the expected traffic volume.

Each worker thread or process is capable of handling one request at a time. When number of incoming requests exceed current number of **worker thread or processes**, Apache may queue or deny additional requests till more worker threads or child processes are available.

It is recommended to set the MaxRequestWorkers value to a value that is equal to or **slightly higher** than the number of concurrent connections you expect your server to handle at peak times. Setting it **too high** can cause **resource exhaustion**, while setting it too low can result in long wait times for users trying to connect to your server.

## Apache Killing Child Process

When an Apache child process reaches the `MaxRequestsPerChild` limit and is terminated, any ongoing requests being handled by that child process are typically not interrupted or lost. Instead, Apache will allow the child process to complete its current requests before terminating it.

Here's how the process typically works:

1. The child process reaches its maximum request limit as defined by `MaxRequestsPerChild`.

2. Apache continues to route new incoming requests to other available child processes that have not reached their request limit.

3. The child process that has reached its limit will continue to handle any ongoing requests until they are completed or timeout.

4. Once all requests being handled by the child process have been completed, the child process is gracefully terminated.

5. Apache creates a new child process to replace the terminated one, ensuring that there are always a sufficient number of child processes to handle incoming requests.

This mechanism ensures that client requests are not abruptly terminated due to child process recycling. Instead, the child process is allowed to finish serving any active requests, which helps prevent service interruptions and maintains a smooth user experience.

Keep in mind that during the process of recycling child processes, there may be a temporary decrease in the overall capacity to handle new requests while new child processes are being created. Properly tuning the `MaxRequestsPerChild` value can help strike a balance between resource management and server performance.

---

## .htaccess

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

**This file must be located in root of directory**.
Example: `/var/www/html/.htaccess`.
**Also the `AllowOverride All` must be set in config file for directory where .htaccess is located.**

The configuration directives found in a **.htaccess** file are applied to the **directory in which the .htaccess file is found, and to all subdirectories** thereof. However, it is important to also remember that there may have been .htaccess files in **directories higher up**. **Directives are applied in the order that they are found. Therefore, a .htaccess file in a particular directory may override directives found in .htaccess files found higher up in the directory tree**. And those, in turn, may have overridden directives found yet higher up, or in the main server configuration file itself.

## Steps -

- Create an **htpasswd** file in system like **htpasswd -c /home/fellowship/.htpasswd user_name**. For this user, it will prompt for **password** which u enter and then it saves it that directory as hidden file.
- Type the following in .htaccess file -

```
AuthType Basic
AuthName "One does not simply"
AuthUserFile /home/fellowship/.htpasswd
Require user username
```

The **AuthType** directive specifies the **authentication type**, which in this case is **Basic**.
The **AuthName** directive specifies the name of the authentication realm, which is displayed to the user when they are prompted for credentials.
The **AuthUserFile** directive specifies the **path to the password file**.
The **Require user** directive specifies the **username** of the user that is allowed to access the protected content.

- Make sure the directory in which this .htaccess file exists has configuration set as -

```
<Directory /var/www/html>
AllowOverride All
</Directory>
```

This **AllowOverride All** allows us to override global config settings with **.htaccess** configuration.

- Add config to **.htaccess** like from here => https://github.com/phanan/htaccess#password-protect-a-directory

**first.conf**

```
<VirtualHost *:8003>
        DocumentRoot /var/www/first
        <Directory /var/www/first >
                AllowOverride All
                Options FollowSymLinks
        </Directory>
</VirtualHost>
```

**/var/www/first/.htaccess**

```
AuthType Basic
AuthName "This is a password protected Directory"
AuthUserFile "/etc/httpd/.htpasswd"
Require user varun
```

It requires authentication of username "varun" with it's password in htpasswd file to start.

## IMPORTANT

**Using `Require valid-user` instead of `Require user varun` will make all users access the directory whose names and passwords match the .htpasswd file**

## Example .htaccess file

```
# Enable the rewrite engine
RewriteEngine On

# Example of URL rewriting:
# Redirect "/products/123" to "/product.php?id=123"
RewriteRule ^products/([0-9]+)$ product.php?id=$1 [L]

# Password protect a directory:
# Require a valid user to access the protected area
AuthType Basic
AuthName "Restricted Area"
AuthUserFile /path/to/.htpasswd
Require valid-user

# Redirect old URLs to new ones
Redirect 301 /old-page.html http://example.com/new-page.html

# Set custom error pages
ErrorDocument 404 /404.html

# Add security headers
Header always set X-Content-Type-Options "nosniff"
Header always set X-Frame-Options "SAMEORIGIN"
Header always set X-XSS-Protection "1; mode=block"

# Enable compression
<IfModule mod_deflate.c>
    AddOutputFilterByType DEFLATE text/html text/plain text/xml
</IfModule>

# Deny access to specific IP addresses or ranges
Deny from 192.168.1.1

# Set caching directives
ExpiresActive On
ExpiresByType text/html "access plus 1 day"
```

---

## htpasswd

https://httpd.apache.org/docs/2.4/programs/htpasswd.html

.htpasswd is a file used by Apache web server to **store usernames and passwords for HTTP authentication**. When a user tries to access a **protected area of a website**, Apache prompts them for a **username and password. The entered credentials are then compared to those stored in the .htpasswd file to determine if the user has access to the protected area**.

The .htpasswd file can be created manually or through the command-line tool **htpasswd**, which is included with most Apache distributions. **The htpasswd tool can add or remove users from the .htpasswd file, as well as change their passwords.**

**The .htpasswd file is often used in conjunction with the .htaccess file to protect specific directories or files within a website**. The .htaccess file specifies which users are allowed to access the protected areas of the website, and can also control other aspects of website access, such as IP address restrictions and redirections.

It's important to ensure that the .htpasswd file is kept **secure**, as it contains sensitive information. The file should be placed outside the web server document root to prevent it from being accessed via the web. Additionally, it's recommended to use strong, complex passwords for each user in the .htpasswd file, and to **encrypt the file** using a secure hashing algorithm such as SHA or bcrypt.

**Use `htpasswd` command line tool like `htpasswd /path/to/htpasswd user1`** to add **user1** and then you will be prompted to add **password** for the given user.

After creating .httpasswd file, we can use it to protect a directory or file and for this we **create .htaccess file in directory you want to protect and add following lines** -

```
AuthType Basic
AuthName "Restricted Area"
AuthUserFile /path/to/htpasswd
Require valid-user
```

This htpasswd file is **encypted** to maintain security and required appropriate file permissions so only authorized users can access.

---

## sites-enabled and sites-available

In Apache, the "**sites-available**" and "**sites-enabled**" directories are used for organizing virtual host configurations, which are used to **host multiple websites on a single Apache server**. Here's what each directory does:

1. **sites-available**: This directory contains configuration files for **available virtual hosts**, which are websites that **can** be hosted on the Apache server. **Each configuration file represents a virtual host and contains directives that define how that virtual host should behave, such as the DocumentRoot, ServerName, and other settings**. These configuration files are created manually or through automated tools and are typically stored in the /etc/apache2/sites-available directory (on Ubuntu/Debian) or /etc/httpd/sites-available directory (on CentOS/RHEL).

2. **sites-enabled**: This directory contains symbolic links to the configuration files of **enabled virtual hosts**. When a virtual host is enabled, the symbolic link to its configuration file is created in the "sites-enabled" directory. This is typically done using the "**a2ensite**" command on Ubuntu/Debian or by manually creating symbolic links on CentOS/RHEL. Apache reads the virtual host configurations from the "**sites-enabled**" directory during runtime, and any changes made to the symbolic links or the actual configuration files will take effect after an Apache restart or reload. **a2dissite** is used to make config file **example1.conf** go from **sites-enabled to sites-available**

The separation of "**sites-available**" and "**sites-enabled**" directories provides a way to **easily enable or disable virtual hosts without modifying the actual configuration files**. It allows for **better organization and management of virtual host configurations**, especially when hosting multiple websites on a single Apache server. By creating and managing virtual host configurations in the "sites-available" directory and enabling or disabling them using symbolic links in the "sites-enabled" directory, you can **easily control which virtual hosts are active at any given time on your Apache server**. We can automate controlling the virtual hosts being active at a given time.

## Conf-available vs Conf-enabled

- **conf-available** - This directory contains configuration files that are **available** for Apache to use but are **not enabled by default**. Each configuration file in "conf-available" typically represents a **module or feature configuration, such as enabling or disabling a specific module, setting global server settings, or defining custom directives**. These configuration files are created manually or through automated tools and are typically stored in the /etc/apache2/conf-available directory (on Ubuntu/Debian) or /etc/httpd/conf-available directory (on CentOS/RHEL).
- **conf-enabled**: This directory contains symbolic links to the configuration files that are enabled and active in the Apache server. When a configuration file is enabled, a symbolic link to its actual configuration file in the "conf-available" directory is created in the "conf-enabled" directory. This is typically done using the "a2enconf" command on Ubuntu/Debian or by manually creating symbolic links on CentOS/RHEL. Apache reads the enabled configuration files from the "conf-enabled" directory during runtime, and any changes made to the symbolic links or the actual configuration files will take effect after an Apache restart or reload.

---

## Virtualhosts

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

## Worker vs Prefork

Worker and prefork are two different **Multi-Processing Modules** (MPMs) in Apache. They differ in the way they handle incoming requests and how they manage processes and threads.

The prefork MPM is the default MPM in Apache 2.2 and earlier versions. It uses a **process-based architecture**, where **each child process can handle one request at a time. When a request arrives, the main Apache process creates a new child process to handle it. Each child process runs independently of the others and has its own memory space**. The prefork MPM is suitable for running **non-thread-safe modules and applications**.

On the other hand, the worker MPM uses a **hybrid process/thread-based architecture**. **It creates multiple child processes, each of which can spawn multiple threads to handle incoming requests. This allows multiple requests to be processed simultaneously within a single child process, reducing the overhead of creating and destroying child processes**. The worker MPM is **more efficient** than the prefork MPM, particularly for **serving large numbers of concurrent requests**. It is also **suitable for running thread-safe applications**.

## Summary

The prefork MPM is **simpler and more stable**, but can be **slower** and **less scalable** than the worker MPM. The worker MPM is **more efficient and scalable**, but can be more **complex to configure and may not be compatible with certain modules and applications**.

---

## Threads in Apache

Threads are **lightweight execution contexts** used to process incoming HTTP requests and each of these worker threads **consume memory which means the number of threads that are created can have an impact on the overall memory usage of the server.**

Each thread is associated with a **single connection**, and is responsible for processing the request and generating a response.

Here we use **worker** module where we can have multiple child processes.

Each thread in Apache requires its own **stack**, which is typically a few kilobytes in size, but can be larger depending on the application and the **amount of stack space that is allocated**. Additionally, each thread **requires memory to hold its context, including any data structures or variables** that are needed to process incoming requests.

## Assigning of Threads

When a new request arrives, Apache assigns the request to an **available worker thread**. The thread then **retrieves the request data from the connection, processes the request, generates the response, and sends the response data back through the connection**.

Using threads allows Apache to handle multiple requests simultaneously, without the need to create a new process for each request.
This can help to improve the performance and scalability of the server, particularly when handling a large number of small requests.

## What Threads really are ?

Threads in Apache can be thought of as individual tasks that can be executed in parallel across the available cores.

Threads of a processor refer to the individual execution contexts that are used by the processor to execute instructions. Each thread represents a separate sequence of instructions that can be executed in parallel with other threads.

Each core on the processor is capable of executing a separate thread, which means that multiple threads can be executed simultaneously, allowing the server to handle multiple requests at once.

For example, imagine a server with a quad-core processor and a configuration that allows up to 100 threads to be created. When requests start arriving, Apache assigns the requests to the available threads, which are distributed across the four cores. Each core then executes its assigned thread, processing the request and generating a response.

---

## Worker Threads vs Child Processes

Apache provides a variety of ways to handle incoming requests, including worker threads and child processes.

In the context of the Apache HTTP Server, "worker threads" and "child processes" refer to different ways of handling incoming client requests concurrently. **Apache supports two main multi-processing modules (MPMs) for handling concurrent connections: the "worker" MPM and the "prefork" MPM. These MPMs determine how Apache creates qand manages threads or processes to handle incoming requests**.

**Worker threads** are **lightweight threads that are spawned by the Apache parent process to handle incoming requests**. They are typically used in a **multi-threaded mode of operation**, where **multiple threads are created to handle concurrent requests**. The advantage of using worker threads is that they are **lightweight, and switching between them is faster than switching between child processes**. **This makes them more efficient for handling a large number of small requests, such as serving static files**.

Child processes, on the other hand, are separate processes that are created by the Apache parent process to handle incoming requests. **Each child process is a complete copy of the parent process, and it runs in a separate address space and has it's own resources like memory, file directives etc**. Child processes are typically used in a multi-process mode of operation, where multiple processes are created to handle concurrent requests. **The advantage of using child processes is that they are isolated from each other, so if one child process crashes or becomes unresponsive, it does not affect the other child processes**. This makes child processes **more suitable for handling larger requests**, such as running dynamic applications like PHP or Python.

---

### Which to Choose When?

In summary, **worker threads are more efficient for handling a large number of small requests, while child processes are more suitable for handling larger requests that require more resources**. The choice between worker threads and child processes ultimately depends on the specific needs of the application being served by Apache.

When a request is received by Apache, the **parent process checks if there is an available child process to handle the request. If there is an available child process, the request is passed to that process. If all child processes are busy, Apache will queue the request until a child process becomes available**.

**Each child process maintains its own request queue and handles requests in a first-in, first-out (FIFO) order.**

**Child processes use a process-based model to handle requests, which means that each process has its own memory space and does not share memory with other processes. This can be an advantage in terms of stability and security because a problem with one process will not affect the other processes. However, it also means that each child process requires its own set of resources, such as CPU and memory, which can make the server more resource-intensive.**

---

## Child Process with Worker Threads

Child processes in Apache **can have worker threads in them**. This is because Apache supports both a multi-process and a multi-threaded mode of operation.

In the multi-process mode of operation, **each child process runs independently of the others and is capable of handling multiple requests concurrently. In this mode, each child process may have multiple worker threads that handle requests in parallel**.

In the **multi-threaded** mode of operation, the **parent process creates a pool of worker threads that handle incoming requests**, rather than creating child processes. This mode can be more efficient than the multi-process mode, as **creating and managing processes can be more resource-intensive than creating and managing threads**.

However, the **multi-process mode can be more stable and secure than the multi-threaded mode, as each child process is isolated from the others and does not share memory. In contrast, threads within a process share memory and can potentially interfere with each other if not properly synchronized**.

---

### Worker MPM:

The worker MPM uses a **multi-threaded approach, where a single parent process creates multiple worker threads**, each of which is **capable of handling one request at a time**. Each worker thread is a **lightweight thread** that **shares the same memory space with the parent process and other worker threads**. This allows for efficient resource utilization as threads can share memory and reduce overhead.
**The worker MPM is generally considered more efficient in terms of memory usage compared to the prefork MPM because threads consume less memory than separate processes.**

### Prefork MPM

The prefork MPM uses a **multi-process approach**, where a **single parent process creates multiple child processes, each of which is a separate instance of the Apache server capable of handling requests independently**.
**Each child process runs in its own memory space, with its own copy of the Apache server and its modules. Child processes do not share memory with each other, which can result in higher memory usage compared to worker threads**. However, child processes are **isolated** from each other, which can provide **better stability and fault tolerance**, as issues in one child process are less likely to impact other child processes.

---

### MinSpareThreads in Apache

### What are MinSpareThreads in Apache?

**A.**
**MinSpareThreads** are **configuration directive** that specifies minimum number of worker threads that should be kept alive in server's thread pool, waiting for handling incoming requests.

When Apache starts, it creates a pool of worker threads that are responsible for processing incoming requests. As requests come in, Apache assigns them to available worker threads in the pool. If there are no idle worker threads available, then the incoming request is put into a queue until a worker thread becomes available.
The **MinSpareThreads** parameter sets the lower limit for the number of idle worker threads that should always be available in the pool. If the number of idle threads falls below this limit, then Apache will create additional worker threads to ensure that the minimum number of idle threads is maintained.

Apache HTTP Server uses multi-process multi-threaded architecture to handle incoming client requests efficiently. These threads are kept "spare" and ready to handle incoming requests helping to **reduce overhead of creating new threads for each incoming requests** to improve server's performance.

When number falls below **MinSpareThreads**, Apache automatically creates new threads upto **MaxRequestWorkers** value which are **maximum number of worker threads that server can create** to handle incoming requests. This ensures server has enough resources to handle incoming traffic efficiently.

**MinSpareThreads** are used in conjunction with **MaxSpareThreads**(maximum number of idle threads to be kept in server thread pool).

These values are configured based on server's hardware resources, anticipated traffic load, performance requirements etc to optimize the server.

---

### Is Threadlimit max number of threads for a child process or server?

**ThreadLimit** is the maximum number of threads that **Apache will create in total**, regardless of whether they are created in a child process or the main server process.

When Apache starts up, it creates **one or more child processes to handle incoming requests** based on value of **StartServer**. **Each child process has its own pool of threads that it can use to handle requests**. The ThreadsPerChild directive specifies the **maximum number of threads that each child process can create**. This number is defined by **ThreadsPerChild** which by default is **25**.

**This values should not be kept too high for excessive resource usage and poor performance, while setting it too low can result in requests being queued or dropped when the server becomes busy.
The optimal value for ThreadsPerChild depends on the available system resources and the expected workload of the server, and it may need to be adjusted over time as the server load changes.**

So, **ThreadLimit is a server-wide setting that limits the total number of threads that can be created across all child processes. When the number of threads in use approaches ThreadLimit, Apache will stop creating new threads, even if there are idle threads available in the thread pool**.

It's important to note that ThreadLimit should be set based on the available system resources and the expected workload of the server. Setting ThreadLimit too low can result in requests being queued or dropped when the server becomes busy, while setting it too high can lead to excessive resource usage and poor performance.

---

### MaxRequestWorkers

**MaxRequestWorkers** is an Apache configuration directive that specifies the maximum number of worker threads that can be created to handle incoming requests. It is used in conjunction with the **ThreadsPerChild** and **MaxClients** directives to control the number of simultaneous connections that the server can handle.

When a client sends a request to the server, a worker thread is created to handle the request. The **MaxRequestWorkers** directive specifies the maximum number of worker threads that can be created to handle incoming requests. If the server reaches this limit, it will stop accepting new connections until existing connections are closed.

Here is an example of how to configure the **MaxRequestWorkers** directive in Apache:

```
<IfModule mpm_worker_module>
    ServerLimit 16
    StartServers 2
    MaxClients 400
    MinSpareThreads 25
    MaxSpareThreads 75
    ThreadsPerChild 25
    MaxRequestWorkers 400
</IfModule>
```

In this example, the **mpm_worker_module** is used as the multi-processing module, which supports thread-based servers. The MaxRequestWorkers directive is set to **400**, which means that the **server can create a maximum of 400 worker threads to handle incoming requests**.

It's important to note that the actual number of worker threads that are created may be lower than the value of MaxRequestWorkers, depending on the server load and other configuration settings. It's also important to ensure that the value of MaxRequestWorkers is set high enough to handle the expected traffic without running out of resources, but not so high that it consumes too much memory or CPU resources.

## MaxRequestWorkers vs ThreadLimit

**MaxRequestWorkers** and **ThreadLimit** are both Apache configuration directives that control the maximum number of worker threads that can be created to handle incoming requests, but they have slightly different purposes.

**MaxRequestWorkers** sets the maximum number of worker threads that can be created to handle incoming requests **across all child processes**. **When this limit is reached, the server will stop accepting new connections until existing connections are closed**. This directive is typically used in conjunction with the **ThreadsPerChild** and **MaxClients** directives to control the number of simultaneous connections that the server can handle.

**ThreadLimit**, on the other hand, sets the **maximum number of worker threads that can be created per child process**. This directive is used in conjunction with the **ThreadsPerChild** directive to control the number of **worker threads that are available to handle incoming requests within each child process**.

Here's an example of how these two directives might be used together:

```
<IfModule mpm_worker_module>
    ServerLimit 16
    StartServers 2
    MaxClients 400
    MinSpareThreads 25
    MaxSpareThreads 75
    ThreadsPerChild 25
    ThreadLimit 64
    MaxRequestWorkers 400
</IfModule>
```

In this example, the **ThreadLimit** directive is set to 64, which means that **each child process can create up to 64 worker threads to handle incoming requests**. The **MaxRequestWorkers** directive is set to 400, which means that the **server can create up to 400 worker threads across all child processes to handle incoming requests**.

It's important to note that the actual number of worker threads that are created may be lower than the values of **ThreadLimit** and **MaxRequestWorkers**, depending on the server load and other configuration settings. It's also important to ensure that **these values are set high enough to handle the expected traffic without running out of resources**, but not so high that they consume too much memory or CPU resources.
