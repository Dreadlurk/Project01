-----Part 1---------------------------------------------------------------------
I installed updates with apt and then installed apache2 
The new .conf file that will be used for the website is webs.conf
The default port for http on apache is port 80.

To find where the default directory is check the .conf file. In the webs.conf file I changed the directory to a new path of /etc/www/webs 

The default config file was 000-default.conf

2 ways that I can confirm that the webserver is serving content is 
going to a browser and seeing if you can connect to the website's IP address (44.207.9.174)(It worked yay!) and see what sites are enabled in /etc/apache2/sites-enabled/


I created a group for the developers called Dev.
When setting up permissions for the Dev group I used this code:
sudo chmod -R 775 webs
This code changes the permissons for the webs file with "-R" being recursive, which changes the subsequent files and folders in the hierarchy.
The 775 means that I the owner can read-write-execute while group can read-write-execute and anyone else can only read-execute.

------Part 2-----------------------------------------------------------------------
I disabled 000-default.conf with a2dissite so it doesn't load over the newer .conf file.

I created a self signed TLS certificate using
 sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/apache-selfsigned.key -out /etc/ssl/certs/apache-selfsigned.crt
 
 
 Edited webs.conf file to redirect all http traffic to go to https using the following code under the virtual host 80:
 Redirect /https://44.207.9.174/
 DocumentRoot/ var/www/webs/
 
 Added a new block for https:
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
ssh tcp port 22 130.108.0.0/16  (allows ssh from WSU networks and leaves bit room for different IPs so whichever IP address I use on the network I can connect.)
ssh tcp port 22 174.103.153.7/32 (allows ssh from my home computer with no bit room for different IPs. This is more strict so that only I can connect from my network.)
http tcp port 80 0.0.0.0/0 (allows any address to use port 80 for http requests)
https tcp port 443 0.0.0.0/0 (allows any address to use port 443 for https requests)

-----Part 4---------------------------------------------------------------------------

When selecting a registrar I would use godaddy.com
The pricing for this would be $2519.99 for the first year
First I would have to find a domain provider and see if my domain would be avaiable.
Next I would have to pay for a plan to have the domain.
Lastly, change the webs.conf file and amend the Servername WSUCardoe.com and add an alias of www.WSUCardoe.com

When selecting a CA to validate my site and generate a certficate I would use comondo
The pricing for this would be $78 per year.
To have ssl we must have a CA validate our site.
First we have a domain and provide proof that we own the website.



-----Part 5 ----------------------------------------------------------------------------


[DIGITAL OCEAN-How to create self signed SSL certs ](https://www.digitalocean.com/community/tutorials/how-to-create-a-self-signed-ssl-certificate-for-apache-in-ubuntu-22-04)


[UBUNTU- HOW TO INSTALL APACHE AND TEST A FILE](https://ubuntu.com/tutorials/install-and-configure-apache#1-overview)




