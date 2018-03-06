# to open my project use "http://54.88.29.132/login"
# to intery to the machine useing file of: authorized_keys
   1- download the a_key file in your machine 
   2- write in terminal >> ssh -i a_key grader@54.88.29.132 -p 2200 >> password "12345"
/////////////////////////////////////////////////////////
# Linux-server-configration
** public_ip: 54.88.29.132 **
 
** ssh_port : 2200 **

* create your vpn instance in my case i used lightsail (ubuntu) they will give you file to connect ssh local in your machine with name : LightsailDefaultPrivateKey-eu-central-1.pem

* open your terminal and write : sudo ssh -vvv -i ~<path to file>/LightsailDefaultPrivateKey-eu-central-1.pem ubuntu@<public_ip>
//////////////////////////////////
#create new user
in your vpn terminal write "sudo adduser grader"

"nano /etc/sudoers.d/grader" >> write "grader ALL=(ALL:ALL) ALL" in file 
then change the dirctory to grader by wirte "su - grader"
//////////////////////////////////////
#  to make more secure 

write "ssh-keygen" in your local machine , then entered the file name , you will get 2 files (the_file_name) & (the_file_name.pub)
go to the (the_file_name.pub) data by using nano or ant editor and copy it
go to your vpn and write this in the terminal
  "su - grader"
  "mkdir .ssh"
  "touch .ssh/authorized_keys"
  "nano .ssh/authorized_keys"
#  past the *.pub* file in the *authorized_keys* file and exit it
#  change the file permissions by write
  "chmod 700 .ssh"
  "chmod 644 .ssh/authorized_keys"
  "service ssh restart"

#  in your machine now you can ssh connect to your instance using line ssh -i <the_file_name_in_.ssh>grader@<public_ip> or by just ssh -v grader@<public_ip> and the os will look for the file in .ssh folder
//////////////////////////////////////////
#  Change ssh default port and add UFW

  "nano sudo vim /etc/ssh/sshd_config" in this file change>> port 2200 or any availabe number
  then exit sudo service ssh restart

  "enable UFW"
  "sudo ufw default deny incoming"
  "sudo ufw default allow outcoming"
  "sudo ufw allow 80/tcp"
  "sudo ufw allow 2200/tcp"
  "sudo ufw allow 123/udp"
  "sudo ufw enable"
#  Update & Upgrade & edit timezone
  "sudo apt-get update"
  "sudo apt-get upgrade"
  "sudo dpkg-reconfigure tzdata">> choose utc for example

#  install apache2 & python wsgi
  "sudo apt-get install apache2"
  "sudo apt-get install libapache2-mod-wsgi"
  "sudo service apache2 restart"
  
#  check your public_ip in the browser ( you will get apache2 default page)
#  install postgres & create the database
  "sudo apt-get install postgresql"

  "sudo su - postgres" change to postgres user

  "psql go to the shell"

    -create the database(data) and user(alaa) then give the user all the permission to it
    -CREATE DATABASE data;
    -CREATE USER alaa;
    -ALTER ROLE alaa WITH PASSWORD 'password';
    -GRANT ALL PRIVILEGES ON DATABASE data TO alaa;
/////////////////////////////////////////////////////////

#  Install git, clone and setup your Catalog App project.
    1-Install Git using sudo apt-get install git
    2-Use cd /var/www to move to the /var/www directory
    3-Create the application directory sudo mkdir FlaskApp
    4-Move inside this directory using cd FlaskApp
    5-Clone the Catalog App to the virtual machine git clone https://github.com/alaashaher/item-catalog-Application
    6-Rename the project's name sudo mv ./ITEM_CATALOG_APP ./FlaskApp
    7-Move to the inner FlaskApp directory using cd FlaskApp
    8-Rename website.py to __init__.py using sudo mv main_page.py __init__.py
    9-Edit DataBase.py, main_page.py and DataBase_operation.py and change engine = create_engine('sqlite:///categories_menu.db') to engine = create_engine('postgresql://alaa:password@localhost/data')
    10-Install pip sudo apt-get install python-pip
    11-Use pip to install dependencies sudo pip install -r requirements.txt
    12-Install psycopg2 sudo apt-get -qqy install postgresql python-psycopg2
    13-Create database schema sudo python database_setup.py
//////////////////////////////////////////////////////////////////
#  create .config file and wsgi file
  "sudo nano /etc/apache2/sites-available/Website.conf"

    then write :

    <VirtualHost *:80>
            ServerName <your_server_dns_or_ip>
            ServerAdmin grader
            WSGIScriptAlias / /var/www/Website/website.wsgi
            <Directory /var/www/Website/Website/>
                    Order allow,deny
                    Allow from all
            </Directory>
            Alias /static /var/www/Website/Website/templates
            <Directory /var/www/Website/Website/templates/>
                    Order allow,deny
                    Allow from all
            </Directory>
            ErrorLog ${APACHE_LOG_DIR}/error.log
            LogLevel warn
            CustomLog ${APACHE_LOG_DIR}/access.log combined
    </VirtualHost>

  #   "sudo a2ensite Website" for enable it

#  add wsgi file in your Website folder write : "sudo nano website.wsgi" then past this :
    #!/usr/bin/python
    import sys
    import logging
    activate_this = 'var/www/Website/Website/venv/bin/activate_this.py'
    execfile(activate_this, dict(__file__=activate_this))

    logging.basicConfig(stream=sys.stderr)
    sys.path.insert(0,"/var/www/Website/")

    from Website import app as application
    application.secret_key = 'Add your secret key'
    restart apache then check (your_server_dns_or_ip).
    sudo service restart apache2

# if you get error check : "sudo tail -f /var/log/apache2/error.log"
