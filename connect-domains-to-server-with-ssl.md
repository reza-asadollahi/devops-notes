Running multiple sites with different domains on a single cloud server is a great way to optimize resources. Here's how you can achieve this, step by step:

1. Set Up Directory Structure
Create separate folders for each website on your server. For example:
```bash
mkdir -p /var/www/site1.com/public_html
mkdir -p /var/www/site2.com/public_html
```
Place the files for each website in their respective ``public_html`` folders.

2. Configure Virtual Hosts
Virtual hosts allow you to host multiple domains on the same server. Depending on your web server (Apache or Nginx), the configuration differs:
For Apache:
Create a configuration file for each domain:
```bash
sudo nano /etc/apache2/sites-available/site1.com.conf
```
Add the following content:
```apache
<VirtualHost *:80>
    ServerName site1.com
    ServerAlias www.site1.com
    DocumentRoot /var/www/site1.com/public_html
    ErrorLog ${APACHE_LOG_DIR}/site1.com-error.log
    CustomLog ${APACHE_LOG_DIR}/site1.com-access.log combined
</VirtualHost>
```
Repeat for site2.com.
Enable the sites and restart Apache:
```bash
sudo a2ensite site1.com.conf
sudo a2ensite site2.com.conf
sudo systemctl restart apache2
```
For Nginx:
Create a configuration file for each domain:
```bash
sudo nano /etc/nginx/sites-available/site1.com
```
Add the following content:
```nginx
server {
    listen 80;
    server_name site1.com www.site1.com;
    root /var/www/site1.com/public_html;

    index index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }
}
```
Repeat for site2.com.
Enable the sites and restart Nginx:
```bash
sudo ln -s /etc/nginx/sites-available/site1.com /etc/nginx/sites-enabled/
sudo ln -s /etc/nginx/sites-available/site2.com /etc/nginx/sites-enabled/
sudo systemctl restart nginx
```

3. Update DNS Records
At your domain registrar, update the DNS settings for each domain:
Add an A Record pointing to your server's IP address.
Optionally, add a CNAME Record for www subdomains.

5. Test Your Setup
Access ``http://site1.com`` and ``http://site2.com`` in your browser to verify that each domain serves the correct content.
Use tools like ping or nslookup to confirm DNS propagation.

7. Optional: Use HTTPS
Secure your sites with SSL certificates using Let's Encrypt. You can install and configure it with Certbot:
```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d site1.com -d www.site1.com
sudo certbot --nginx -d site2.com -d www.site2.com
```
This setup ensures that each domain is isolated in its own folder while sharing the same server resources. Let me know if you need help with any specific step! 🚀
