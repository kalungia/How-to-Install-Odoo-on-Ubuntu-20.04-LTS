# How to Install Odoo 17 on Ubuntu 20.04 LTS
A complete Guide on How to Install Odoo 17 on Ubuntu 20.04 LTS

### STEP 1 ~ Update and Upgrade Packages
    sudo apt-get update -y
    sudo apt-get upgrade -y
    
### STEP 2 ~ Create a New User
    sudo sudo adduser --system --home=/opt/[odoo17] --group [odoo17]
    
### STEP 3 ~ Fail2ban
    sudo apt-get install openssh-server fail2ban
    
### STEP 4 ~ Install Necessary Packages
#### Install Wkhtmltopdf
    sudo wget https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.5/wkhtmltox_0.12.5-1.bionic_amd64.deb
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

### STEP 4 ~ Create and Setup Database
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
### STEP 5 ~ Create System User
    sudo adduser --system --home=/opt/[odoo17] --group [odoo17]

### STEP 6 ~ Clone Odoo17 From Github

#### Install git
    sudo apt-get install git
    
#### Change to the system user to [odoo17]
    sudo su - [odoo17] -s /bin/bash
    
#### Clone Odoo 17
    git clone https://www.github.com/odoo/odoo --depth 1 --branch 17.0 --single-branch .
    
#### Once Done Exit From System User
    exit
    
### STEP 7 ~ Run Requirements File
    sudo pip3 install -r /opt/[odoo17]/requirements.txt

### STEP 8 ~ Setup Conf File
#### Copy /opt/odoo17/debian/odoo.conf file to /etc.
    sudo cp /opt/[odoo17]/debian/odoo.conf /etc/[odoo17].conf
    sudo nano /etc/[odoo17].conf

#### Update the Conf File you have copied in /etc
    [options]
       ; This is the password that allows database operations:
       admin_passwd = admin
       db_host = False
       db_port = False
       db_user = [odoo17]
       db_password = False
       addons_path = /opt/[odoo17]/addons
       logfile = /var/log/[odoo17]/[odoo17].log
       
#### Grant System user [odoo17] access to the conf file
    sudo chown [odoo17]: /etc/[odoo17].conf
    sudo chmod 640 /etc/[odoo17].conf
    
### STEP 9 ~ Create an [odoo17] Log Directory and Set Permissions
    sudo mkdir /var/log/[odoo17]
    sudo chown [odoo17]:root /var/log/[odoo17]

### STEP 10 ~ Service file and Start Odoo 17
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
       ExecStart=/opt/[odoo17]/odoo-bin -c /etc/[odoo17].conf
       [Install]
       WantedBy=default.target
       
#### Set the root user's permissions for this service file
    sudo chmod 755 /etc/systemd/system/[odoo17].service
    sudo chown root: /etc/systemd/system/[odoo17].service

#### Start Odoo17
    sudo systemctl start [odoo17].service

## Vist Your Site
    http://<your_domain_or_IP_address>:8069
