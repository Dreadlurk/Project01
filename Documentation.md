-----Part 1---------------------------------------------------------------------

The default port for http on apache is port 80.

To find where the default directory use the following code: 
cd /var/www/

The primary config file for this is 000-default.conf

2 ways that I can confirm that the webserver is serving content is 
going to a browser and seeing if you can connect to the website's IP address (44.207.9.174) and see what sites are enabled in /etc/apache2/sites-enabled/



When setting up permissions for the Dev group I used this code:
sudo chmod -R 775 webs
This code changes the permissons for the webs file with "-R" being recursive, which changes the subsequent files and folders in the hierarchy.
The 775 means that I the owner can read-write-execute while group can read-write-execute and anyone else can only read-execute.

------Part 2-----------------------------------------------------------------------
 first disabled 000-default.conf with a2dissite
 
 created a self signed TLS certificate using
 sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/apache-selfsigned.key -out /etc/ssl/certs/apache-selfsigned.crt
 
 
 made a new .conf file and edited the following under the virtual host 80:
 Redirect /https://44.207.9.174/
 DocumentRoot/ var/www/webs/
 
 then add a new block for https:
 <VirtualHost *:443>
   ServerName wsucardoe.com
   DocumentRoot /var/www/webs/webss

   SSLEngine on
   SSLCertificateFile /etc/ssl/certs/apache-selfsigned.crt
   SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key
</VirtualHost>

The location of the cert is under: /etc/ssl/certs/apache-selfsigned.crt
The location of the key is udner: /etc/ssl/private/apache-selfsigned.key

To restart the system so changes can be made is:
sudo systemctl apache 2 reload

Confirmed that https is running when I went on my browser and saw the https address.

------Part 3------------------------------------------------------------------------

In the EC2/Security-groups I changed the following inbound rules
ssh tcp port 22 130.108.0.0/16  (allows ssh from WSU networks and leaves bit room for different IPs)
ssh tcp port 22 174.103.153.7/32 (allows ssh from my home computer with no bit room for different IPs)
http tcp port 80 0.0.0.0/0 (allows any address to use port 80 for http requests)
https tcp port 443 0.0.0.0/0 (allows any address to use port 443 for https requests)

-----Part 4---------------------------------------------------------------------------

When selecting a registrar I would use godaddy.com
The pricing for this would be $2519.99 for the first year

When selecting a CA to validate my site and generate a certficate I would use comondo
The pricing for this would be $78 per year.







