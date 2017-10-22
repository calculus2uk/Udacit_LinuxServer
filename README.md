# Project 6 Linux Server Configuration
The 6th and final project in the udacity FullStack Nano degree (FSND).

## Table of Content
 - Title and Intro
 - Description
 - Purpose
 - IP Address, SSH and URL
 - Summary of Configuration
 - Create user Grader
 - Configure the local timezone to UTC
 - Install and configure Apache to serve a Python mod_wsgi application
 - Install and configure PostgreSQL
 - Deploy the Item Catalog project
 - Lincense
 - Credits

## Description
In this project, a brand-new, bare bones, Linux server is turned into a secure and efficient web application host that the item catalog application. The Item catalog is a web app developed earlier in the course.

## Purpose
The purpose of this project is to learn how to access, secure, perform the initial configuration of a bare-bones Linux server, and configure a web and database server to actually host a web application.Also go deeper on exactly what web applications are doing, how they are hosted and how they interact between multiple systems. Understanding these are key qualities of a Full Stack Developer.

## IP Address and SSH Port
```sh
IP Address: 18.194.170.170 
Port 2200
Apache2 80
the complete url is http://18.194.170.170/
```

## Summary of Configuration
 - #### Get Server
        - Installed Git for windows so I can use the Git GUI terminal
        - Start a new Ubuntu Linux server instance on Amazon Lightsail.
    
 - #### SHH into Server
    - Followed the instructions 
     [here](https://classroom.udacity.com/nanodegrees/nd004/parts/ab002e9a-b26c-43a4-8460-dc4c4b11c379/modules/357367901175462/lessons/3573679011239847/concepts/c4cbd3f2-9adb-45d4-8eaf-b5fc89cc606e) to ssh into server.
    Regarding the instructions, I downloaded the SSH from the AWS account page and ssh into     the sever using the default Ubuntu user like so
`ssh -i ~/.ssh/key.pem ubuntu@18.194.170.170`

- #### Secure the Server
  - I updated the currently installed packages 
    ```sh
    sudo apt-get update
    sudo apt-get upgrade
    ```
  - Change the SSH port from 22 to 2200
    - `sudo nano /etc/ssh/sshd_config`  and added Port 2200 to listening ports. I did         this to avoid being locked out. I then went to the networking tab on my AWS account       page to add the custom/tcp port 2200 to the list.
    - `sudo service ssh restart ` to restart my server and then exit it.
    - I then try to login into the server again by typing in `ssh -i ~/key.pem               ubuntu@18.194.213.77 -p 2200` to see if I can sucesfully log into the      port 2200     before I disable the port 22.
  - Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH       (port 2200), HTTP (port 80), and NTP (port 123)
    - sudo ufw status. This is to check whether its active or not.
    - sudo ufw default deny incoming: Deny incoming 
    - sudo ufw default allow outgoing. Allow outgoing
    - sudo ufw allow ssh
    - sudo ufw allow 2200/tcp
    - sudo ufw allow www
    - sudo ufw allow ntp
    - sudo ufw enable
 
 #### Create a new user called grader and give access
 - create user
    ``` sh 
    sudo adduser grader
    ```

 - Give grader the permission to sudo.
In the /etc/sudoers.d, create a new file called grader
   ```sh 
    $ sudo nano /etc/sudoers.d/grader        
    In the file put in: grader ALL=(ALL) NOPASSWD:ALL
    ```
 - Save and qiut.
 - Create an SSH key pair for grader
    ``` sh
     - Using the ssh-keygen tool. Run ssh-keygen on my local machine.
    ```
    
    to see and copy the key
    ```
    cat ~/.ssh/graderKeyP.pub
    ```
    Back on ther server 
    ```
    su grader
    cd ..
    mkdir .ssh
    sudo nano .ssh/authorized_keys
    ```
    Paste the copied key here.Save and quite.
    
    Set specific file permission on the authorized key files and the ssh directory. Thisisa     security measure that SSHenforces to ensure other users cannot gain access to the           account.
    Run
    ```
    chmod 700 .ssh
    Chmod 644 .ssh/authorized_keys
    ```
 #### Configure the local timezone to UTC
 - Run
    ```
    Run sudo dpkg-reconfigure tzdata
    ```
    scroll to the bottom of the Continents list and select Etc or None of the above; in the second list, select UTC
 #### Install and configure Apache to serve a Python mod_wsgi application
 ```
 sudo apt-get update
 sudo apt-get install libapache2-mod-wsgi-py3
 ```
 #### Install and configure PostgreSQL
 ```
sudo apt-get update
sudo apt-get install postgresql postgresql-contrib
sudo -i -u postgres to login into postgres account
run createuser –interactive and set all answers to questions asked to no (Don’t allow any  privilege)
ALTER ROLE catalog WITH PASSWORD 'catalog'
createdb catalog
run psql and then ALTER DATABASE catalog OWNER TO catalog;
 ```
 #### Deploy the Item Catalog project.
 - Run
    ```
        sudo apt-get update
        sudo a2enmod wsgi
        mkdir item_catalog
        cd item catalog 
    ```
- and then clone my previous project from git [here](https://github.com/calculus2uk/udacity_CatalogApp.git)
- Edit `application.py `file to `__init__.py` with sudo mv command.
 
 - install py3 development
    ```
    sudo apt-get install python-pip
    ```
 - install virtual environment named venv
     ```
     sudo pip install virtualenv
     ```
 - create a virtual environment called venv and add permission to it to enable internal installation
    ```
    sudo virtualenv venv
    sudo chmod -R 777 venv
    
    ```
 - activate the virtual environment
    ```
    source venv/bin/activate
    ```
 - install the following dependencies

    ```
    	pip install Flask 
        pip install sqlachemy 
        pip install requests
        pip install psycopg2
        pip install tree
        pip install --upgrade google-api-python-client
    ```
- I updated the occurances of client_secrets.json by adding ful path (/var/www/CatalogApp/CatalogApp/client_secrets.json)to all occurances in the __init__.py

 - Edit the `__init__.py` and `database_setup.py` file:
Change engine = create_engine('sqlite:///categoryitems.db') to engine = create_engine('postgresql://catalog:catalog@localhost/catalog')

 - Restart Apache
    ```
     $ sudo service apache2 restart 
    ```
 - The third party logins was not working so I added the ip address to the devlopers console.(google and facebook)

## Lincense
MIT License

Copyright (c) [2017] [Isaac Yao Tuagba]

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.


## Credits

The Udacity team
[Sportsdirect](Sportsdirect.com)
[askubuntu](https://askubuntu.com/questions/168280/how-do-i-grant-sudo-privileges-to-an-existing-user)
[Digital Ocean](https://www.digitalocean.com/community/tutorials/how-to-create-a-sudo-user-on-ubuntu-quickstart)
[Ask Ubuntu](https://askubuntu.com/questions/138423/how-do-i-change-my-timezone-to-utc-gmt)
[Digital Ocean](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-16-04)
[StackOverflow](https://stackoverflow.com/questions/4313323/how-to-change-owner-of-postgresql-database)
[Udacity](https://discussions.udacity.com/t/tips-for-configuring-postgresql-and-setting-up-item-catalog-project/223436)
[StackOverflow](https://stackoverflow.com/questions/24525588/how-to-install-flask-on-python3-using-pip)
[Dital Ocean](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)
[cyberciti.biz](https://www.cyberciti.biz/faq/linux-rename-file/)
[Ask Ubuntu](https://askubuntu.com/questions/431251/how-to-print-the-directory-tree-in-terminal)




