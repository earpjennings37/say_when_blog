# 1. New ways to spend my Bennies ($$$) w/a AWS Account, ayyyy

- your pockets are getting stacked now!

# 2. Create an RDS instance for the MySQL database

# 3. Create an EC2 instance for the WordPress application

- ssh -i wordpress-key.pem ec2-user@public-ip-address
a. Couple command line examples
- wget https://wordpress.org/latest.tar.gz
- tar -xzf latest.tar.gz
- sudo chown -R apache:apache /var/www/html/
- sudo find /var/www/html/ -type d -exec chmod 755 {} \;
- sudo find /var/www/html/ -type f -exec chmod 644 {} \;
- sudo nano /var/www/html/wp-config.php

# 4. Install and configure WordPress on EC2

- Just like that it was done & could check into the blog & AWS to see the specimen.... ANNNNND then I tore it down. 
- Why? Cuz I was intrigued by the other options available & see the other avenues to create a blog. 
- I don't have a favorite, but as mentioned above I'll have posts about how to create a WordPress blog in the handful of options above. 
- Yeah, even some Chat GPT action, stay tuned

# 5. Upload and download files to and from S3

# 6. Access your WordPress site from the internet