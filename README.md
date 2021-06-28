# MERN Crib Sheet
A crib sheet for setting up a web app using MongoDB, Express, Node and React


A step-by-step guide based on ["The Dead-Simple Step-By-Step Guide for Front-End Developers to Getting Up and Running With Node.JS, Express, and MongoDB"](https://closebrace.com/tutorials/2017-03-02/the-dead-simple-step-by-step-guide-for-front-end-developers-to-getting-up-and-running-with-nodejs-express-and-mongodb)
by Christopher Buecheler  


The crib sheet uses [Embedded JavaScript (EJS)](https://ejs.co).
EJS is a simple templating language that lets you generate HTML markup with plain JavaScript.
The Express default templating language is Jade, but this has been deprecated in favour of Pug, which is an indentation-based markup.

The name of the project for this crib_sheet is ```nodetest1```

##Install node and NPM

Install the latest version from the [Node.js](https://nodejs.org/en/)

If there are any issues with permissions, use ```sudo chown``` to set the superuser ownership:

```
sudo chown -R $USER /usr/local/lib/node_module
```

##Install Express

```
npm install -g express-generator
```

##Create an Express Project

Navigate to the folder that will store the project.

The name of the project for this crib sheet is ```nodetest1```

Note that the command below also specifies that views will be rendered with EJS.

```
express --view="ejs" nodetest1
```

##Install MongoDB and Monk

Use npm to isntall MongoDB and Monk.

The ```--save``` command will add them to the package.json dependancies.

```
npm install --save monk
npm install --save mongodb
```

##Install all dependancies

Use ```npm install``` to install all of the dependancies listed in the pacakge.json file

This should create a ```node_modules``` directory

##App is now created

Run ```npm start```

Go to ```http://localhost:3000```

There should be a "Welcome to Express" page

**app.js**

This is the heart of the app.

It creates a number of javascript variables and links them to dependancies, packages, node functions and routes.

It instantiates Express and assigns the app to it:

```JavaScript
var app = express();
```

It also tells the app where to find its views:

```JavaScript
app.set('views', path.join(__dirname, 'views'));
```

And specifies which engine to use to render the views (in this example, EJS):

```JavaScript
app.set('view engine', 'ejs');
```

The rest of the code sets a number of conditions to get the app up and running.

It also tells the app to make it look as though content is coming from the top level:

```JavaScript
app.use(express.static(path.join(__dirname, 'public')));
```

The line above means that even though images are located in ```nodetest1\public\images``` they are accessed via ```localhost:3000/images```.


Finally, app.js exports the app object so that Node can call it elsewhere in the code:

```JavaScript
module.exports = app;
```


**index.js**

The top-level route should be index.js, found in the routes folder: ```nodetest1\routes\index.js```

The initial code here:
- requires the Express functionality with ```var express = require('express');```
- then attaches a "router" variable to Express's router method using ```var router = express.Router();```

It uses ```router``` when an attempt is made to HTTP get the top level directory of the website.

Note: router is then exported back to the app with ```module.exports = router;```

##Add new page

To add another page, add the following to the index.js file before the export command at the end:

```JavaScript
/* GET Hello World page. */
router.get('/helloworld', function(req, res) {
  res.render('helloworld', { title: 'Hello, World!' });
});
```

Go to the Views folder and save a version of ```index.ejs``` renamed as ```helloworld.ejs```

Note that the ```title``` variable is set in ```index.js```

If the server is still running, stop it using ```CTRL-C```

run ```npm start``` again and visit ```http://localhost:3000/helloworld/``` to view the new page.

Note: changes to EJS templates do not require a server restart, but changes to a js file, such as app.js or the route files, require a restart.

##Set up MongoDB

It's easiest to set up MongoDB using Homebrew.

Check it's up to date first using ```brew update```

Install the mongo 'tap': ```brew tap mongodb/brew```

Then install mongodb-community (the free version of mongodb): ```brew install mongodb-community@4.4```

Create a database in which MongoDB can store its data.

MongoDB suggests saving it a folder called ```data``` and naming the file ```db```

start MongoDB as a service:

```
brew services start mongodb-community
```

##Run Mongo

Run the server then, in a separate terminal window, type ```mongo```.

It should launch the Mongo client, report the MongoDB Shell version and connect to the database server. (If it fails, try restarting the computer.)

Tell Mongo which database to use - for this cribsheet it's nodetest1:

```
use nodetest1
```


The command below will create a collection called ```usercollection``` within the ```db``` that it is using (```nodetest1```). It will then append an entry to that collection and add a unique identifier ID number for the entry.

```
db.usercollection.insert({ "username" : "testuser1", "email" : "testuser1@testdomain.com" })
```

If it's worked, Mongo will respond with ```WriteResult({ "nInserted" : 1 })```

Use ```db.usercollection.find().pretty()``` to display what's in it. (the ```.pretty()``` command adds line breaks to the output )

Mongo can also define an array with multiple objects then append it to a collection, eg:

```
newstuff = [{ "username" : "testuser2", "email" : "testuser2@testdomain.com" }, { "username" : "testuser3", "email" : "testuser3@testdomain.com" }]
db.usercollection.insert(newstuff);
```

##Connect Node to MongoDB

Tell ```app.js``` to use Monk to talk to Mongo and that the database is located at ```localhost:27017/nodetest1```:

27017 is the default port for MongoDB

Define the following at the top of the ```app.js``` file.

```JavaScript
var monk = require('monk');
var db = monk('localhost:27017/nodetest1');
```

Furtherdown the file are some `app.use` statements for Express.

```JavaScript
app.use('/', indexRouter);
app.use('/users', usersRouter);
```

They provide custom functions that the rest of the app can use.

The db `app.use` statements need to come before the route `app.use`, since the routes will make use of the db.

Add the following above the 'indexRouter' and 'usersRouters' statements detailed above.

```JavaScript
// Make our db accessible to our router
app.use(function(req,res,next){
    req.db = db;
    next();
});
```
