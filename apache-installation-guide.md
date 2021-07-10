## 1. Setup your domain
*Make sure you have a wildcard DNS record pointing at your server.*

### A. If you are using *[Google Domains](https://domains.google.com):*
1. Create a DDNS record for `app.yourdomain.com` since we cannot create wildcard records like `*.yourdomain.com` :thinking:. 
But don't worry, we are going to fix that in the next step. Update your router by entering the credentials Google Domains provides for the DDNS record.

2. Now we add the wildcard `CNAME` record and point it to the record we created in the previous step :point_up_2:. 
As you can see, it doesn't really matter what you name your server subdomain name. 
Here, I used `app`. You can change it to `whatever.yourdomain.com`

Host name | Type | TTL | Data
----------|------|-----|-----
*.yourdomain.com | CNAME | 1 hour | app.yourdomain.com.

## 2. Configure your router
So far, the requests going to `*.yourdomain.com` is pointed to `app.yourdomain.com` and that is pointed to our router.
Now, we need to forward the incoming requests on both ports `80` and `443` to our server. 

*:radioactive:	We need to set a static IP for our server so we can always find it.*

## 3. 
