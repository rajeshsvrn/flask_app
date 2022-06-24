# flask_app

How to deploy python application on apache2 aws ubuntu instance
========================================================================
sudo apt-get update
sudo apt-get install apache2
sudo apt-get install libapache2-mod-wsgi
sudo apt-get install libapache2-mod-wsgi-py3

=========================================================================
sudo apt install python3.8
sudo apt-get install python-pip3
sudo pip3 install flask
pip install flask-mysql

==========================================================================

clone the repo from git hub : # https://github.com/rajeshsvrn/flask_app.git and link to it from the site-root defined in apache’s configuration(/var/www/html by default, see /etc/apache2/sites-enabled/000-default.conf for the\ current value).

sudo ln -sT ~/flask_app /var/www/html/flask_app

==========================================================================

Create .wggi in the flash_app dir

#flaskapp.wsgi import sys
sys.path.insert(0, '/var/www/html/flask_app')

#file and app name
from app import app as application

==========================================================================
#updating config file

/etc/apache2/sites-enabled/000-default.conf

        WSGIDaemonProcess flaskapp threads=5
        WSGIScriptAlias / /var/www/html/flask_app/flaskapp.wsgi
          <Directory flask_app>
	 WSGIProcessGroup app
	 WSGIApplicationGroup %{GLOBAL}
    	 Order deny,allow
	 Allow from all
      </Directory>
======================================================================
$ sudo service apache2 restart

=======================================================================\

$ sudo tail -f /var/log/apache2/error.log


Creating database and sql procedure call

Enter the required password and, when logged in, execute the following command to create the database:

1
CREATE DATABASE BucketList;
Once the database has been created, create a table called tbl_user as shown:


CREATE TABLE `BucketList`.`tbl_user` (
  `user_id` AUTO_INCREMENT,
  `user_name` VARCHAR(45) NULL,
  `user_username` VARCHAR(45) NULL,
  `user_password` VARCHAR(45) NULL,
  PRIMARY KEY (`user_id`));
  
  
We'll be using Stored procedures for our Python application to interact with the MySQL database. So, once the table tbl_user has been created, create a stored procedure called sp_createUser to sign up a user.

When creating a stored procedure to create a user in the tbl_user table, first we need to check if a user with the same username already exists. If it exists, we need to throw an error to the user, otherwise we'll create the user in the user table. Here is how the stored procedure sp_createUser would look:

DELIMITER $$
CREATE PROCEDURE `sp_createUser`(
    IN p_name VARCHAR(20),
    IN p_username VARCHAR(20),
    IN p_password VARCHAR(20)
)
BEGIN
    if ( select exists (select 1 from tbl_user where user_username = p_username) ) THEN
      
        select 'Username Exists !!';
      
    ELSE
      
        insert into tbl_user
        (
            user_name,
            user_username,
            user_password
        )
        values
        (
            p_name,
            p_username,
            p_password
        );
      
    END IF;
END$$
DELIMITER ;


Creating a Stored Procedure
To validate a user, we'll need a MySQL stored procedure. So create a MySQL stored procedure as shown:

DELIMITER $$
CREATE PROCEDURE `sp_validateLogin`(
IN p_username VARCHAR(20)
)
BEGIN
    select * from tbl_user where user_username = p_username;
END$$
DELIMITER ;



