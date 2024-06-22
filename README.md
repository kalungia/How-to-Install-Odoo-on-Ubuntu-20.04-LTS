# How to Install Odoo 17 on Ubuntu 20.04 LTS
A complete Guide on How to Install Odoo 17 on Ubuntu 20.04 LTS

### STEP 1 ~ Update and Upgrade Packages
    sudo apt-get update -y
    sudo apt-get upgrade -y
    
### STEP 2 ~ Create a New User
    sudo sudo adduser --system --home=/opt/[odoo17] --group [odoo17]

 

### Step 3 ~ Install Necessary Dependencies
Install the necessary dependencies.
 
    sudo apt install -y software-properties-common

### Step 4 ~ Add the deadsnakes PPA (which contains newer Python versions)
Add the deadsnakes PPA to install Python 3.10.
 
    sudo add-apt-repository ppa:deadsnakes/ppa
    sudo apt update

### Step 5 ~ Install Python 3.10 & pip for Python 3.10
Install Python 3.10.

    sudo apt install -y python3.10

    sudo apt install -y python3.10-venv python3.10-dev
    curl -sS https://bootstrap.pypa.io/get-pip.py | sudo python3.10

 #### Verify the Python Installation
  Ensure Python 3.10 is installed correctly.
  You should see something like Python 3.10.x.

    python3.10 --version
 

 

    
### STEP 6 ~ Fail2ban
    sudo apt-get install openssh-server fail2ban

    
### STEP 7 ~ Install Necessary Packages

#### Install Wkhtmltopdf
    sudo wget https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.5/wkhtmltox_0.12.5-1.bionic_amd64.deb
    chmod -R 777 wkhtmltox_0.12.5-1.bionic_amd64.deb
    sudo dpkg -i wkhtmltox_0.12.5-1.bionic_amd64.deb
    sudo apt install -f
#### Install Python Packages
    sudo apt-get install -y python3-pip
    sudo apt-get install python-dev python3-dev libxml2-dev libxslt1-dev zlib1g-dev libsasl2-dev libldap2-dev build-essential libssl-dev libffi-dev libmysqlclient-dev libjpeg-dev libpq-dev libjpeg8-dev liblcms2-dev libblas-dev libatlas-base-dev

#### Install Node.js and NPM
    sudo apt-get install -y npm
    sudo ln -s /usr/bin/nodejs /usr/bin/node
    sudo npm install -g less less-plugin-clean-css
    sudo apt-get install -y node-less

### STEP 8 ~ Create and Setup Database
#### Install PostgreSQL is the database server used by Odoo
    sudo apt-get install postgresql

#### Create Database User
    sudo su - postgres
    createuser --createdb --username postgres --no-createrole --no-superuser --pwprompt [odoo17]

#### The user and the password are needed for the conf file. Postgres uses a distinct system user to perform tasks. 
#### To switch between users, run sudo su -postgres. Next, create a database user for Odoo 17.
    psql
    ALTER USER [odoo17] WITH SUPERUSER;
    \q
    exit
### STEP 9 ~ Create System User
    sudo adduser --system --home=/opt/[odoo17] --group [odoo17]

### STEP 10 ~ Clone Odoo17 From Github

#### Install git
    sudo apt-get install git
    
#### Change to the system user to [odoo17]
    sudo su - [odoo17] -s /bin/bash
    
#### Clone Odoo 17
    sudo mkdir /opt/[odoo17]
    git clone https://www.github.com/odoo/odoo --depth 1 --branch 17.0 --single-branch /opt/[odoo17]
    
#### Once Done Exit From System User
    exit
    
#### Step 11 ~ Verify the Python Installation
Ensure Python 3.10 is installed correctly.

    sudo -s
    python3.10 --version



<br/><br/>

---

<br/><br/>

## If you want to use venv - Virtual Environment then follow these extra steps

#### Step 11.1 ~ Create a Virtual Environment for Odoo 17 (Optional but Recommended)
Create a virtual environment for Odoo 17.

    python3.10 -m venv /opt/[odoo17]/venv


#### Step  11.2 ~ Activate the Virtual Environment
Activate the virtual environment & Install Odoo dependencies within it. 
After you finish deactivate it.

    source /opt/[odoo17]/venv/bin/activate
    
    sudo pip3 install -r /opt/[odoo17]/requirements.txt
    
    deactivate
    
<br/><br/>

---

<br/><br/>

#### Step 12 ~ Install Odoo Dependencies ~ Run Requirements File

 
    sudo pip3 install -r /opt/[odoo17]/requirements.txt
    


### STEP 13 ~ Setup Conf File
#### Copy /opt/odoo17/odoo17/debian/odoo.conf file to /etc.
    sudo cp /opt/[odoo17]/debian/odoo.conf /etc/[odoo17].conf
    sudo nano /etc/[odoo17].conf

#### Update the Conf File you have copied in /etc
    [options]
       ; This is the password that allows database operations:
       ; Edit admin_passwd and set your master password there !
       admin_passwd = admin
       db_host = False
       db_port = False
       db_user = [odoo17]
       db_password = False
       proxy_mode = True 
       addons_path = /opt/[odoo17]/addons
       logfile = /var/log/[odoo17]/[odoo17].log

       
#### Grant System user [odoo17] access to the conf file
    sudo chown [odoo17]: /etc/[odoo17].conf
    sudo chmod 640 /etc/[odoo17].conf
    
### STEP 14 ~ Create an [odoo17] Log Directory and Set Permissions
    sudo mkdir /var/log/[odoo17]
    sudo chown [odoo17]:root /var/log/[odoo17]

### STEP 15 ~ Service file and Start Odoo 17
#### We have to create a service to run Odoo 17. Let’s create a service file ‘[odoo17].service’ in /etc/systemd/system.
    sudo nano /etc/systemd/system/[odoo17].service
    
#### Add the following aspects to the newly created service file.
    [Unit]
       Description=Odoo17
       Documentation=http://www.odoo.com
    [Service]
       # Ubuntu/Debian convention:
       Type=simple
       User=[odoo17]
       # if you are using venv then use this ExecStart :
       # ExecStart=/opt/[odoo17]/venv/bin/python /opt/[odoo17]/odoo-bin -c /etc/[odoo17].conf
       #
       ExecStart=/usr/bin/python3.10 /opt/[odoo17]/odoo-bin -c /etc/[odoo17].conf
    [Install]
       WantedBy=default.target 

       
#### Set the root user's permissions for this service file
    sudo chmod 755 /etc/systemd/system/[odoo17].service
    sudo chown root: /etc/systemd/system/[odoo17].service

#### Start Odoo17
    sudo systemctl start [odoo17].service
    
### Reload systemd and restart Odoo service
    sudo systemctl daemon-reload
    sudo systemctl restart odoo17
    sudo systemctl enable odoo17
    sudo systemctl status odoo17

## Vist Your Site
    http://<your_domain_or_IP_address>:8069


<br/><br/><br/>

---

<br/><br/><br/>


# Setting up Apache2 to Reverse Proxy Odoo on a VPS

This guide will help you set up Apache2 to reverse proxy Odoo on a VPS using the server's IP address.

### Step 1: Install Apache2

If you haven't already installed Apache2, you can do so by running the following commands:

    sudo apt update
    sudo apt install apache2

### Step 2: Enable Necessary Apache Modules

You need to enable some modules in Apache to use it as a reverse proxy:

    sudo a2enmod proxy
    sudo a2enmod proxy_http
    sudo a2enmod headers
    sudo a2enmod rewrite

After enabling these modules, restart Apache to apply the changes:

    sudo systemctl restart apache2

### Step 3: Configure Apache for Odoo

Create a new configuration file for your Odoo setup in Apache:

    sudo nano /etc/apache2/sites-available/odoo.conf

Add the following configuration to the file. Replace `YOUR_SERVER_IP` with your VPS's IP address:


```
<VirtualHost *:80>
    ServerAdmin admin@yourdomain.com
    ServerName YOUR_SERVER_IP

    ProxyRequests Off
    ProxyPass / http://localhost:8069/
    ProxyPassReverse / http://localhost:8069/

    <Proxy *>
        Order deny,allow
        Allow from all
    </Proxy>

    <Location />
        Order allow,deny
        Allow from all
    </Location>

    ErrorLog ${APACHE_LOG_DIR}/odoo_error.log
    CustomLog ${APACHE_LOG_DIR}/odoo_access.log combined
</VirtualHost>
```

Save and close the file.

### Step 4: Enable the Odoo Site Configuration

Enable the site configuration and disable the default site:

    sudo a2ensite odoo.conf
    sudo a2dissite 000-default.conf


Step 5: Restart Apache

To apply your new configuration, restart Apache:

    sudo systemctl restart apache2

### Step 6: Accessing Odoo

You should now be able to access Odoo using your VPS's IP address in a web browser. Simply type `http://YOUR_SERVER_IP` in your browser's address bar, where `YOUR_SERVER_IP` is your VPS's IP address.


### Step 7: Configure the Firewall

Check Firewall Status: First, check if the firewall is active on your Ubuntu server. The default firewall for Ubuntu is UFW (Uncomplicated Firewall). Check its status with:

    sudo ufw status

Allow Apache Through Firewall: Since you are using Apache as a reverse proxy for Odoo, you need to allow Apache through the firewall. Apache registers a few profiles with UFW. You can allow traffic on `port 80 (HTTP)` with the following command:

    sudo ufw allow 'Apache'

Enable the Firewall: If the firewall is not active, enable it with:

 
    sudo ufw enable

Check the Updated Firewall Status: Finally, check the status again to ensure your rules are active:


    sudo ufw status

### Step 8: Fix Fully Qualified Domain Name (FQDN) Warning

If you encounter the `warning AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1. Set the 'ServerName' directive globally`, follow these steps:

Open the Apache2 Configuration File:

    sudo nano /etc/apache2/apache2.conf

Set the Global ServerName: Add a line at the bottom of this file with your server's IP address or a dummy FQDN. For example:

    ServerName YOUR_SERVER_IP

Replace `YOUR_SERVER_IP` with your `server's public IP address`.

Save and Close the File: After adding the line, save the file and exit the editor.

Restart Apache: To apply the changes, restart Apache:

     sudo systemctl restart apache2

Check the Status Again: Finally, check the status of Apache again to ensure the warning is gone:

 
    systemctl status apache2

This should resolve the warning about the server's FQDN.

Additional Notes
Accessing Odoo: You can now access Odoo using your server's IP address: `http://YOUR_SERVER_IP/web`

Firewall Considerations: If you're accessing the server via SSH, make sure to allow SSH (port 22) through the firewall before enabling it to avoid locking yourself out of the server:

    sudo ufw allow ssh


<br/><br/><br/>    
