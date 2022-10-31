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
   CREATE USER myprojectuser WITH PASSWORD 'password'
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

### Create a Django app and deploy it on the server with Nginx.












