P5 - Linux Server Administration and Catalog App Deployment

Summary: The this file describes Project 5 of the Udacity Nanodegree -- Linux Server Administration. The project involves configuring an Amazon Web Services Ubuntu server and deploying the Catalog App completed in Project 3 of the Udacity Nanodegree. The application has been populated with sample data from the populate_database.py file (as part of the application files on the server.)


Reviewing the Project: The project has been deployed to the AWS server. The url and IP address pointing to the deployed application are listed below to view and use the Catalog App. To view the server configuration and make any modifications to the server or application, it will be necessary to ssh login to the server via the "grader" username. The password and port have been provided below. The private key is not posted in this README file for security reason. Those who have access to the private key will be able to complete the login process and modify the server and deployed application.


PROJECT IP ADDRESS AND SSH PORT

I.P. Address: 52.35.158.254
Port 2200

To login, ssh with the following command:

ssh grader@52.35.158.254 -p 2200 -i ~/.ssh/grader_key -v
password: "password"

Note that the grader_key (also referred to as the udacity_key) has been made available separately to the reviewer and not uploaded to Github for security protections. The file path may need to be adjusted based on where it is stored on the local machine of the grader user.



URL TO HOSTED APPLICATION

http://52.35.158.254/ OR

http://ec2-52-35-158-254.us-west-2.compute.amazonaws.com/category/35/



INSTALLED SOFTWARE AND PACKAGES

ntp [for UTC and local time configuration]
sudo apt-get install ntp

apache2 [web server]
sudo apt-get install apache2

python tools
sudo apt-get install python-setuptools

python adapter module for Apache
sudo apt-get install libapache2-mod-wsgi

git tools for cloning repository for catalog app
sudo apt-get install git

pip installer
sudo apt-get install python-pip

Not quite sure what is does but it was a dependency to get other apps installing correctly-- it is some type of complier for higher level programming languages.
sudo apt-get install gcc

virtual environment to isolate running web application
sudo apt-get install virtualenv

The following software was added to the virtual environment:

flask -- lightweight python framework for catalog app
pip install Flask

httplib2 to handle requests from catalog app
pip install httplib2

requests to handle web requests
pip install requests

oauth2client -- for authorization and authentication with Google login
sudo pip install --upgrade oauth2client

SQLAlchemy -- ORM for postgres
sudo pip insatll sqlalchemy

psycopg -- python posgres adapter
*had trouble installing from package via sudo apt-get install python-psycopg2
instead used python package manager
pip install psycopg2

postgresql database
sudo apt-get install postgresql postgresql-contrib



CONFIGURATION OF WEB SERVER

-Allowed ssh server to listen on Port 2200 (instead of default Port 22)
-Disabled login by "root" (user); allowed login by created user "grader"
-Provided grader user with root-like privileges to read, write and execute via sudo command
-Enabled login with a private RSA key
-Updated and upgraded all existing install packages
-Modified the UFW (firewall) to allow incoming connection requests for SSH (Port 2200), HTTP (port 80), and NTP (port 123) and disallow all others. All outgoing connection requests are allowed.
-Set local timezone to Universal Time Coordinated (this was actually the default setting on the server)
-To run the catalog python web application, installedd and configure all of the packages and software listed in the prior section.
-Installed and setup the Postgresql database which stores the data from the catalog application. A new user "catalog" was also created on the server with permissions to access the application database.



ADJUSTMENTS to the CATALOG APP PROJECT FILES:

ADJUSTMENT NO. 1 Full path for client_secrets file

When running the application, the Apache server produced the following error:

[Sat Jan 02 22:41:55.098325 2016] [:error] [pid 30038:tid 140055003817728] [client 98.14.228.227:50304] IOError: [Errno 2] No such file or directory: 'client_secrets.json'

This error was a result of the server not being able to find the client_secrets.json file. The application was modified such that the "views.py" file replaced the two references to the client_secrets.json file with the full path '/var/www/catalog/client_secrets.json'


ADJUSTMENT NO. 2 Modfiy the database path

To create the database with Postgresql, it was necessary to modify the create_engine block of code which allows the application to log into and access the corresponding database. Because we setup a new database with it user and password in psql (postgresql), we needed to modify the existing code.

engine = create_engine('postgresql:///model') #'model' was the original database name submitted in Projecct 3.

This block is replaced with the following--
engine = create_engine('postgresql://catalog:password@localhost/catalog')

This adjustment is necessary in the files 'views.py', 'database_setup.py', and 'populate_database.py'.


ADJUSTMENTS NO. 3 Running the file from __init__.py

In the original Project 3 Catalog application submission, the application was designed to run by initializing the application via the 'runserver.py' file. For Project 5 because of the deployment on the Apache server, the application must startup with the default configuration of the server that looks to the '__init__.py' file to initialize the application.

Here, the '__init__.py' file removes the docstrings from around the initializing block [with app.run(host='0.0.0.0')] was always present but not utilized in the Project 3 submission.

Also in the file, a note was printed to the log as part of the initialization sequence stating that "The application should be serving on port 9080." This port number was associated with the previous localhost in Project 3 but is not relevant to AWS deployment.

ADJUSTMENTS NO. 4 Oauth2 credentials

For the Google authorization sequence to work correctly, you will need to modify the allowed javascript origins and redirect URIs. You can do this via the console.developers.google.com management area for the Catalog application. Once you have added your AWS URL to the list of allowed javascript origins, you must modify your "client_secrets.json" file since it contains this information. The revised file should make the newly downloaded json secrets file from the Google credentials/javascript origins page.



LIST OF SOME RESOURCES USED to complete this project

http://flask.pocoo.org/
http://flask.pocoo.org/docs/0.10/deploying/mod_wsgi/

http://www.jakowicz.com/flask-apache-wsgi/

http://unix.stackexchange.com/questions/153564/apache2-not-reading-sites-enabled-conf

https://github.com/stueken/FSND-P5_Linux-Server-Configuration

http://www.cyberciti.biz/faq/howto-use-grep-command-in-linux-unix/

http://www.howtogeek.com/howto/42980/the-beginners-guide-to-nano-the-linux-command-line-text-editor/

http://askubuntu.com/questions/47515/any-way-to-search-for-text-within-nano

http://initd.org/psycopg/docs/install.html

http://stackoverflow.com/questions/27195490/cannot-install-psycopg2-on-virtualenv

http://stackoverflow.com/questions/14391804/e-unable-to-find-a-source-package-for-psycopg2-or-use-pip-with-virtualenv

https://blog.openshift.com/how-to-install-and-configure-a-python-flask-dev-environment-deploy-to-openshift/

http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world

http://www.linuxquestions.org/questions/ubuntu-63/e-unable-to-locate-package-870440/

http://www.cyberciti.biz/faq/mv-command-howto-move-folder-in-linux-terminal/

http://superuser.com/questions/46139/what-does-source-do

http://bash.cyberciti.biz/guide/Source_command

Udacity forums

http://gcc.gnu.org/


