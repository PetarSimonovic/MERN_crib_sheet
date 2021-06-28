# MERN Crib Sheet
A crib sheet for setting up a web app using MongoDB, express, node and React


A step-by-step guide based on ["The Dead-Simple Step-By-Step Guide for Front-End Developers to Getting Up and Running With Node.JS, Express, and MongoDB"](https://closebrace.com/tutorials/2017-03-02/the-dead-simple-step-by-step-guide-for-front-end-developers-to-getting-up-and-running-with-nodejs-express-and-mongodb)
by Christopher Buecheler  


The crib sheet uses [Embedded JavaScript (EJS)](https://ejs.co).
EJS is a simple templating language that lets you generate HTML markup with plain JavaScript.
The Express default templating language is Jade, but this has been deprecated in favour of Pug, which is an indentation-based markup.

The name of the project for this crib_sheet is ```nodetest1```

**Install node and NPM**

Install the latest version from the [Node.js](https://nodejs.org/en/)

If there are any issues with permissions, use ```sudo chown``` to set the superuser ownership:

```
sudo chown -R $USER /usr/local/lib/node_module
```

**Install Express**

```
npm install -g express-generator
```

**Create an Express Project**

Navigate to the folder that will store the project.

The name of the project for this crib sheet is ```nodetest1```

Note that the command below also specifies that views will be rendered with EJS.

```
express --view="ejs" nodetest1
```

**Install MongoDB and Monk**

Use npm to isntall MongoDB and Monk.

The ```--save``` command will add them to the package.json dependancies.

```
npm install --save monk
npm install --save mongodb
```

**Install all dependancies**

Use ```npm install``` to install all of the dependancies listed in the pacakge.json file

This should create a ```node_modules``` directory

**Test the app**

Run ```npm start```

Go to ```http://localhost:3000```

There should be a "Welcome to Express" page

**app.js**

This is the heart of the app.

It creates a number of javascript variables and links them to dependancies, packages, node functions and routes.

It instantiates Express and assigns the app to it:
```
var app = express();
```

It also tells the app where to find its views:
```
app.set('views', path.join(__dirname, 'views'));
```

And specifies which engine to use to render the views (in this example, EJS):
```
app.set('view engine', 'ejs');
```

The rest of the code sets a number of conditions to get the app up and running.


**index.js**

The top-level route should be index.js, found in the routes folder: ```nodetest1\routes\index.js```
