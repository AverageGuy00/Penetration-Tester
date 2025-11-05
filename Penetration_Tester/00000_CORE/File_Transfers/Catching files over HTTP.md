Web transfer is the default path because `HTTP` and `HTTPS` are almost always allowed through `firewalls`. Bonus: `HTTPS` usually encrypts files in transit, which keeps sensitive material from being exposed on the wire. Getting caught on a `penetration test` pushing a `password dump` to external infrastructure over plaintext and triggering the client’s `IDS` is a fast way to look reckless. Use encrypted transport whenever possible.

## Nginx - Enabling PUT

A good alternative for transferring files to `Apache` is [Nginx](https://www.nginx.com/resources/wiki/) because the configuration is less complicated, and the module system does not lead to security issues as `Apache` can.

When allowing `HTTP` uploads, it is critical to be 100% positive that users cannot upload web shells and execute them. `Apache` makes it easy to shoot ourselves in the foot with this, as the `PHP` module loves to execute anything ending in `PHP`. Configuring `Nginx` to use PHP is nowhere near as simple.

#### Create a Directory to Handle Uploaded Files

```shell
OathBornX@htb[/htb]$ sudo mkdir -p /var/www/uploads/SecretUploadDirectory
```

#### Change the Owner to www-data

```shell
OathBornX@htb[/htb]$ sudo chown -R www-data:www-data /var/www/uploads/SecretUploadDirectory
```

#### Create Nginx Configuration File

Create the Nginx configuration file by creating the file `/etc/nginx/sites-available/upload.conf` with the contents:

```shell
server {
    listen 9001;
    
    location /SecretUploadDirectory/ {
        root    /var/www/uploads;
        dav_methods PUT;
    }
}
```

#### Symlink our Site to the sites-enabled Directory

```shell
OathBornX@htb[/htb]$ sudo ln -s /etc/nginx/sites-available/upload.conf /etc/nginx/sites-enabled/
```

#### Start Nginx

```shell
OathBornX@htb[/htb]$ sudo systemctl restart nginx.service
```

If we get any error messages, check `/var/log/nginx/error.log`. If using Pwnbox, we will see port 80 is already in use.

#### Verifying Errors

```shell
OathBornX@htb[/htb]$ tail -2 /var/log/nginx/error.log

2020/11/17 16:11:56 [emerg] 5679#5679: bind() to 0.0.0.0:`80` failed (98: A`ddress already in use`)
2020/11/17 16:11:56 [emerg] 5679#5679: still could not bind()
```

```shell
OathBornX@htb[/htb]$ ss -lnpt | grep 80

LISTEN 0      100          0.0.0.0:80        0.0.0.0:*    users:(("python",pid=`2811`,fd=3),("python",pid=2070,fd=3),("python",pid=1968,fd=3),("python",pid=1856,fd=3))
```

```shell
OathBornX@htb[/htb]$ ps -ef | grep 2811

user65      2811    1856  0 16:05 ?        00:00:04 `python -m websockify 80 localhost:5901 -D`
root        6720    2226  0 16:14 pts/0    00:00:00 grep --color=auto 2811
```

We see there is already a module listening on port 80. To get around this, we can remove the default Nginx configuration, which binds on port 80.

#### Remove NginxDefault Configuration

```shell
OathBornX@htb[/htb]$ sudo rm /etc/nginx/sites-enabled/default
```

Now we can test uploading by using `cURL` to send a `PUT` request. In the below example, we will upload the `/etc/passwd` file to the server and call it users.txt

#### Upload File Using cURL

```shell
OathBornX@htb[/htb]$ curl -T /etc/passwd http://localhost:9001/SecretUploadDirectory/users.txt
```

```shell
OathBornX@htb[/htb]$ sudo tail -1 /var/www/uploads/SecretUploadDirectory/users.txt 

user65:x:1000:1000:,,,:/home/user65:/bin/bash
```

Verify directory listing is disabled by visiting `http://localhost/SecretUploadDirectory`. By default `Apache` will auto-list a folder that lacks an `index.html`, which exposes `sensitive` files — exactly what you don’t want when `exfilling`. `Nginx` doesn’t enable that behavior by default. Disable `directory listing`, add an `index.html`, and enforce access controls (authentication/`HTTPS`) to reduce `IDS` noise and accidental exposure.