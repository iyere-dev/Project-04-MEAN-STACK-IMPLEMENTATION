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


`vi server.js`

![Server.js configuration code](./images/installing_mongodb/server.js_configuration.png)


INSTALL EXPRESS AND SET UP ROUTES TO THE SERVER

Express is a minimal and flexible Node.js web application framework that provides features for web and mobile applications. We will use Express to pass book information to and from our MongoDB database.

We also will use Mongoose package which provides a straight-forward, schema-based solution to model your application data. We will use Mongoose to establish a schema for the database to store data of our book register


`sudo npm install express mongoose`

![installing express and mongoose](./images/express_and_routes_setup/installing_express_and_mongoose.png)

In Books folder, create  a folder named apps and change directory to apps

`mkdir apps && cd apps`

Create a file named routes.js and copy and paste file below into it:


const Book = require('./models/book');

module.exports = function(app){
  app.get('/book', function(req, res){
    Book.find({}).then(result => {
      res.json(result);
    }).catch(err => {
      console.error(err);
      res.status(500).send('An error occurred while retrieving books');
    });
  });

  app.post('/book', function(req, res){
    const book = new Book({
      name: req.body.name,
      isbn: req.body.isbn,
      author: req.body.author,
      pages: req.body.pages
    });
    book.save().then(result => {
      res.json({
        message: "Successfully added book",
        book: result
      });
    }).catch(err => {
      console.error(err);
      res.status(500).send('An error occurred while saving the book');
    });
  });

  app.delete("/book/:isbn", function(req, res){
    Book.findOneAndRemove(req.query).then(result => {
      res.json({
        message: "Successfully deleted the book",
        book: result
      });
    }).catch(err => {
      console.error(err);
      res.status(500).send('An error occurred while deleting the book');
    });
  });

  const path = require('path');
  app.get('*', function(req, res){
    res.sendFile(path.join(__dirname, 'public', 'index.html'));
  });
};




`vi routes.js`

![routes.js configuration](./images/express_and_routes_setup/routes.js_configuration_file.png)

in apps folder, create a folder named models and cd into models

`mkdir models && cd models`

create a file named book.js then copy and paste code below into it:

var mongoose = require('mongoose');
var dbHost = 'mongodb://localhost:27017/test';
mongoose.connect(dbHost);
mongoose.connection;
mongoose.set('debug', true);
var bookSchema = mongoose.Schema( {
  name: String,
  isbn: {type: String, index: true},
  author: String,
  pages: Number
});
var Book = mongoose.model('Book', bookSchema);
module.exports = mongoose.model('Book', bookSchema);


`vi books.js`

![books.js configuration](./images/express_and_routes_setup/books.js_configuration.png)


ACCESS THE routes WITH AngularJS

AngularJS provides a web framework for creating dynamic views in your web applications. In this tutorial, we use AngularJS to connect our web page with Express and perform actions on our book register.

Change the directory back to ‘Books’

`cd ../..`

Create a folder named 'public' and cd into 'public'

`mkdir public && cd public`

Add file named script.js and paste code below (controller configuration defined) into it:

var app = angular.module('myApp', []);
app.controller('myCtrl', function($scope, $http) {
  $http( {
    method: 'GET',
    url: '/book'
  }).then(function successCallback(response) {
    $scope.books = response.data;
  }, function errorCallback(response) {
    console.log('Error: ' + response);
  });
  $scope.del_book = function(book) {
    $http( {
      method: 'DELETE',
      url: '/book/:isbn',
      params: {'isbn': book.isbn}
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
  $scope.add_book = function() {
    var body = '{ "name": "' + $scope.Name + 
    '", "isbn": "' + $scope.Isbn +
    '", "author": "' + $scope.Author + 
    '", "pages": "' + $scope.Pages + '" }';
    $http({
      method: 'POST',
      url: '/book',
      data: body
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
});


`vi script.js`

![script.js configuration](./images/express_and_routes_setup/script.js_configuration_code.png)

In public folder, create a file named index.html and paste code below into it:

<!doctype html>
<html ng-app="myApp" ng-controller="myCtrl">
  <head>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
    <script src="script.js"></script>
  </head>
  <body>
    <div>
      <table>
        <tr>
          <td>Name:</td>
          <td><input type="text" ng-model="Name"></td>
        </tr>
        <tr>
          <td>Isbn:</td>
          <td><input type="text" ng-model="Isbn"></td>
        </tr>
        <tr>
          <td>Author:</td>
          <td><input type="text" ng-model="Author"></td>
        </tr>
        <tr>
          <td>Pages:</td>
          <td><input type="number" ng-model="Pages"></td>
        </tr>
      </table>
      <button ng-click="add_book()">Add</button>
    </div>
    <hr>
    <div>
      <table>
        <tr>
          <th>Name</th>
          <th>Isbn</th>
          <th>Author</th>
          <th>Pages</th>

        </tr>
        <tr ng-repeat="book in books">
          <td>{{book.name}}</td>
          <td>{{book.isbn}}</td>
          <td>{{book.author}}</td>
          <td>{{book.pages}}</td>

          <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
        </tr>
      </table>
    </div>
  </body>
</html>



`vi index.html`

![index.html configuration](./images/express_and_routes_setup/index.html_configuration_code.png)

Change back to Books directory

`cd ..`

start server by running command below:

`node server.js`

![Server up and running](./images/express_and_routes_setup/node_server.js_response_server_running.png)

The server is now up and running, we can connect it via port 3300. You can launch a separate Putty or SSH console to test what curl command returns locally.

`curl -s http://localhost:3300`

![curl response](./images/express_and_routes_setup/curl_response.png)

Open TCP port 3300 in AWS console to be able to access url vis browser

![opening TCP port 3300](./images/express_and_routes_setup/opening_TCP_port_3300.png)

Access book reister app in browser

![Accessing book register app in browser](./images/express_and_routes_setup/accessing_app_in_browser.png)

END OF PROJECT!
