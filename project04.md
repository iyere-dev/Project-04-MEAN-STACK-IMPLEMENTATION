#  implementing a simple Book Register web form using MEAN stack
## INSTALL NODEJS

Node.js is a JavaScript runtime built on Chrome’s V8 JavaScript engine. Node.js is used in this tutorial to set up the Express routes and AngularJS controllers.

update ubuntu

`sudo apt update`

![updating ubuntu](./images/installing_nodejs/updating_ubuntu.png)

upgrade ubuntu

`sudo apt upgrade -y`

Add certificates

`sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates`

![adding certificates](./images/installing_nodejs/adding_certificates1.png)

`curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash -`

![adding certificates](./images/installing_nodejs/adding_certificates2.png)

install nodejs

`sudo apt install -y nodejs`

![installing nodejs](./images/installing_nodejs/installing_nodejs.png)

### INSTALL MONGODB

MongoDB stores data in flexible, JSON-like documents. Fields in a database can vary from document to document and data structure can be changed over time. For our example application, we are adding book records to MongoDB that contain book name, isbn number, author, and number of pages.

Import the Public Key used by Package Management System

`sudo apt-get install gnupg curl`

![importing public key used by package management system step 1](./images/installing_mongodb/importing_public_key1.png)

`curl -fsSL https://pgp.mongodb.com/server-6.0.asc | sudo gpg -o /usr/share/keyrings/mongodb-server-6.0.gpg --dearmor`

![importing public key used  for package management system step 2](./images/installing_mongodb/importing_public_key_step2.png)

Create a list file for MONGODB

FOR UBUNTU 20.04

`echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-6.0.gpg ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list`


FOR UBUNTU 22.04

`echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-6.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list`


We are using UBUNTU 20.04

![creating a list file for mongoDB](./images/installing_mongodb/creating_list_file_for_mongodb.png)

Reload local packages

`sudo apt-get update`

![reloading local packages](./images/installing_mongodb/reloading_local_packages.png)

Install MongoDB

`sudo apt-get install -y mongodb-org`

![installing mongoDB](./images/installing_mongodb/installing_mongoDB_page1.png)

![installing mongoDB](./images/installing_mongodb/installing_mongoDB_page2.png)

Start and enable mongoDB service

`sudo systemctl start mongod`

`sudo systemctl enable mongod`

![starting and enabling mongoDB service](./images/installing_mongodb/startin_and_enabling_mongoDB_service.png)

Verify that MongoDB service is up and running

`sudo systemctl status mongod`

![Verify that MongoDB service is up and running](./images/installing_mongodb/mongodb_service_up_and_running.png)

Install body-parser package

We need ‘body-parser’ package to help us process JSON files passed in requests to the server

`sudo npm install body-parser`

![installing body parser](./images/installing_mongodb/installing_body-parser.png)

Create a folder named ‘Books’ and cd into Books

`mkdir Books && cd Books`

In the books directory, Initialise npm project

`npm init`

![Initialising npm project](./images/installing_mongodb/initialising_npm_project.png)

Add file named srever.js and paste configuration below:


var express = require('express');
var bodyParser = require('body-parser');
var app = express();
app.use(express.static(__dirname + '/public'));
app.use(bodyParser.json());
require('./apps/routes')(app);
app.set('port', 3300);
app.listen(app.get('port'), function() {
    console.log('Server up: http://localhost:' + app.get('port'));
});


![Server.js configuration code](./images/installing_mongodb/server.js_configuration.png)




`vi server,js`

