---
sidebar_position: 2
---

### Create password file for Basic Authentication

**Create the Password File Using the OpenSSL Utilities:**

We are using `sammy` as our username, but you can use whatever name you’d like:
```bash
sudo sh -c "echo -n 'sammy:' >> /etc/nginx/.htpasswd"
```

After running above command, you must enter a password

Next, add an encrypted password entry for the username by typing:
```bash
sudo sh -c "openssl passwd -apr1 >> /etc/nginx/.htpasswd"
```

You can see how the usernames and encrypted passwords are stored within the file by typing:
```bash
cat /etc/nginx/.htpasswd

# output
sammy:$apr1$wI1/T0nB$jEKuTJHkTOOWkopnXqC1d1
```

### Configure Nginx Password Authentication
- We have already had a file with our users and passwords. We need to configure Nginx to check this file before serving content.

Open config file, example here is `default`:

```bash
sudo nano /etc/nginx/sites-enabled/default
```

We will use the `auth_basic_user_file` directive to point Nginx to the password file we created:

```bash
server {
    listen 80 default_server;
    listen [::]:80 default_server ipv6only=on;

    root /usr/share/nginx/html;
    index index.html index.htm;

    server_name localhost;

    location / {
        try_files $uri $uri/ =404;
        auth_basic "Restricted Content";
        auth_basic_user_file /etc/nginx/.htpasswd;
    }
}
```

Save and close the file when you are finished. Restart Nginx to implement your password policy:

```bash
sudo service nginx restart
```

**Reference**: https://www.digitalocean.com/community/tutorials/how-to-set-up-password-authentication-with-nginx-on-ubuntu-14-04