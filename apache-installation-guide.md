## 1. Setup your domain
*Make sure you have a wildcard DNS record pointing at your server.*

### 1.A. If you are using *[Google Domains](https://domains.google.com):*
#### 1.A.1. Create a DDNS record for `app.yourdomain.com` since we cannot create wildcard records like `*.yourdomain.com` :thinking:. 
But don't worry, we are going to fix that in the next step. Update your router by entering the credentials Google Domains provides for the DDNS record.

#### 1.A.2. Now we add the wildcard `CNAME` record and point it to the record we created in the previous step :point_up_2:. 
As you can see, it doesn't really matter what you name your server subdomain name. 
Here, I used `app`. You can change it to `whatever.yourdomain.com`

Host name | Type | TTL | Data
----------|------|-----|-----
*.yourdomain.com | CNAME | 1 hour | app.yourdomain.com.

*[Link](https://serverfault.com/questions/670066/google-dynamic-dns-wildcard-subdomains) to the reference*

## 2. Configure your router
So far, the requests going to `*.yourdomain.com` is pointed to `app.yourdomain.com` and that is pointed to our router.
Now, we need to forward the incoming requests on both ports `80` and `443` to our server. 

*:radioactive:	We need to set a static IP for our server so the router can always find it.*

## 3. Prepare the server
### 3.1. Install `apache2` on your server.

`sudo apt install apache2`

*[Link](https://ubuntu.com/tutorials/install-and-configure-apache#1-overview) to the reference*

### 3.2. Enable the necessary mods by running the followig commands:

```
sudo a2enmod proxy
sudo a2enmod proxy_http
sudo a2enmod proxy_balancer
sudo a2enmod lbmethod_byrequests
sudo service apache2 reload
```

*[Link](https://serverfault.com/questions/195611/how-do-i-redirect-subdomains-to-a-different-port-on-the-same-server) to the reference*

### 3.3. Create an apache config file for your service

#### 3.3.1. Go to `/etc/apache2/sites-available`

#### 3.3.2. Create a file called `subdomain.conf`. For example, the file name will be `api.service1.conf` if you are creating a subdomain for `api.service1.yourdomain.com`

#### 3.3.3. Add the following content to the file and add the new service to apache using `sudo a2ensite api.service1.conf` command:
```
<VirtualHost *:80>
	ServerAdmin admin@yourdomain.com
	ServerName api.service1.yourdomain.com
        ProxyPreserveHost On
        ProxyPass / http://STATIC_IP:PORT/
        ProxyPassReverse / http://STATIC_IP:PORT/
RewriteEngine on
RewriteCond %{SERVER_NAME} =api.service1.yourdomain.com
RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,NE,R=permanent]
</VirtualHost>
```
Change `STATIC_IP` to the server's IP and `PORT` to the application port.

#### 3.3.4. Validate that apache has picked the changes using `a2query -s` command.

### 3.4. Install Certbot :robot: on your server and follow the instructions.

`sudo certbot --apache`

In the interactive installation steps, use `api.service1.yourdomain.com.` domain.

### 3.5. Run your local server
Test to make sure it is reachable by using the static IP address created for the server.
