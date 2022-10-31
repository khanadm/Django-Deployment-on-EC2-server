# Django-Deployment-on-EC2-server

#### Description:

- Launch an EC2 server.
- Install the dependencies to run the Django app.
- Create a Django app and deploy it on the server with Nginx.
- Attach Domain to it.
- Read about the file structure and use of each file in Django.
- Use Postgres as a database.

---

#### First step: Create an EC2 server

  - Firstly, log in to the AWS management console.
  - After login, navigate to the search bar, type EC2, and select EC2
  - Now, the EC2 dashboard will appear. Click Instances
  - Click the Launch instances button.
  - To launch an EC2 instance, a few details are required, i.e., instance name, OS image (AMI), instance type, etc.
  - Select a key pair to attach with the instance to log in with that key.
  - Click the Launch instance button
  - Take the public IP from the EC2 dashboard and use it to login inside the instance using ssh.
  - Finally, it will be logged in successfully if everything is configured correctly.

---

####  Second step: Install the dependencies to run the Django app.

If you are using Django with Python 3, type:

- Firstly, we need to update local apt package index using

   ```sh
   sudo apt-get update
   ```
   
   
 - This will install pip, the Python development files needed to build Gunicorn later, the Postgres database system and the libraries needed to              interact  with it, and the Nginx web server.
 
- Log into an interactive Postgres session by typing:

  ```sh
  sudo -u postgres psql
  ```
  
- First, create a database for your project:

   ```sh
   CREATE DATABASE myproject
   ```
  
- Next, create a database user for our project. Make sure to select a secure password

   ```sh
   CREATE USER postgres WITH PASSWORD '123456'
   ```
  
- Now, we can give our new user access to administer our new database

  ```sh
  GRANT ALL PRIVILEGES ON DATABASE myproject TO myprojectuser
  ```

- To check postgreSQL version use 

  ```sh
  psql -V
  ```


  ```sh
  sudo apt-get install python3-pip python3-dev libpq-dev postgresql postgresql-contrib nginx
  ```
     

- Upgrade pip and install the package by typing:


   ```sh
   sudo -H pip3 install --upgrade pip
   ```


- Insatll a Python Virtual Environment for My Project  


   ```sh
   sudo -H pip3 install virtualenv
   ```


---

### Create a Django app and deploy it on the server with Nginx & Attach Domain to it

Create and move into a directory where we can keep our project files.

```sh
mkdir data
``` 

```sh
cd data
```

Activate you virtual enviroment 

```sh
source myvenv/bin/activate
```

With your virtual environment active, install Django, Gunicorn, and the psycopg2 PostgreSQL adaptor with the local instance of pip:

```sh
pip install django gunicorn psycopg2
```

Now create a django project in the project directory using 

```sh
django-admin.py startproject useraccount
```

-  Adjust the Project Settings.


  We have to make changes in setting.py file. In ALLOWED_HOSTS section we have to write the server's address or domain names may be used to connect to      Django app.
  

![allowed host in setting.py](https://user-images.githubusercontent.com/106643382/198976359-7d1f5fc0-ecc5-46dd-a42d-f5090418ea6d.png "allowed host in setting.py")


Next in DATABASES section change the setting with postgreSQL database information. We need to tell Django to use the psycopg2 adapter. Give database name, the database username, the database user’s password, and then specify that the database is located on the local computer.


![Database information](https://user-images.githubusercontent.com/106643382/198979509-65f34a03-9025-4678-9958-57c947126a3c.png "Database information")

Complete Initial Project Setup

Now, we can migrate the initial database schema to our Postgres database using the management script:

```sh
useraccount/manage.py makemigrations
```
```sh
useraccount/manage.py migrate
```
Create an exception for port 8000 by typing

```sh
sudo ufw allow 8000
```

Finally, you can test our your project by starting up the Django development server with this command

```sh
useraccount/manage.py runserver 0.0.0.0:8000
```
![sample login page](https://user-images.githubusercontent.com/106643382/198988099-407c2208-fb51-4b01-8e52-4ac565e5634b.png "sample login page")

We’re now finished configuring our Django application. We can back out of our virtual environment by typing

```sh
deactivate
```

Create a Gunicorn systemd Service File

```sh
sudo vim /etc/systemd/system/gunicorn.service
```
![gunicorn service file](https://user-images.githubusercontent.com/106643382/198989048-ad3249fd-0a06-4d35-a30c-e4aca3a7ad0e.png "gunicorn service file")


Create a Gunicorn systemd Shocket File In this location.

```sh
sudo vim /etc/systemd/system/gunicorn.socket
```

![Shocket file](https://user-images.githubusercontent.com/106643382/199003079-010a23e9-68a7-4a6e-a452-fedb1c0e45d4.png "Shocket file")


We can now start the Gunicorn service & socket file we created and enable it so that it starts at boot

```sh
sudo systemctl start gunicorn
```

```sh
sudo systemctl enable gunicorn
```

Check the status of the process to find out whether it was able to start

```sh
sudo systemctl status gunicorn
```

![gunicorn service status](https://user-images.githubusercontent.com/106643382/198992762-b93b4b90-d129-42f4-af13-c7e666b68a9e.png "gunicorn service status")



```sh
sudo systemctl start gunicorn.socket
```

```sh
sudo systemctl enable gunicorn.socket
```

```sh
sudo systemctl status gunicorn.socket
```

![Status gunicorn socket](https://user-images.githubusercontent.com/106643382/199006019-12da07c6-47fd-4660-acb6-d5dadab25eef.png "Status gunicorn socket")


I have made a daemon service file because after reboot of application it will work smoothly.
 
 In this location 
 
```sh
sudo /lib/systemd/system
```

```sh
sudo vim myscriptlogin.service
```

![daemon file](https://user-images.githubusercontent.com/106643382/198991205-19d8b868-aa13-4f09-810e-1cfede41c1f4.png "daemon file")

```sh
sudo systemctl start myscriptlogin.service
```

```sh
sudo systemctl enable myscriptlogin.service
```

```sh
sudo systemctl status myscriptlogin.service
```

![deamon status](https://user-images.githubusercontent.com/106643382/198993629-134f9e79-f590-416b-82c1-30f94dec7a57.png "deamon status")

If you make changes to the /etc/systemd/system/gunicorn.service file, reload the daemon to reread the service definition and restart the Gunicorn process by typing:

```sh
sudo systemctl daemon-reload
```

```sh
sudo systemctl restart gunicorn
```

#### Configuring Nginx as a reverse proxy

Create a configuration file for Nginx using the following command

```sh
sudo vim /etc/nginx/sites-available/mydata
```

![nginx setup proxy](https://user-images.githubusercontent.com/106643382/199008133-dbef8630-ab5f-4431-b390-7438daa5feb5.png "nginx setup proxy")

Finally, we’ll create a location / {} block to match all other requests. Inside of this location, we’ll include the standard proxy_params file included with the Nginx installation and then we will pass the traffic to the socket that our Gunicorn process created:


Activate the configuration using the following command

```sh
sudo ln -s /etc/nginx/sites-available/mydata etc/nginx/sites-enabled/
```

Test your Nginx configuration for syntax errors by typing:

```sh
sudo nginx -t
```

If no errors are reported, go ahead and restart Nginx by typing:

```sh
sudo systemctl restart nginx
```


#### THANK YOU
















