# MERN Crib Sheet
A crib sheet for setting up a web app using MongoDB, express, node and React


A step-by-step guide based on ["The Dead-Simple Step-By-Step Guide for Front-End Developers to Getting Up and Running With Node.JS, Express, and MongoDB"](https://closebrace.com/tutorials/2017-03-02/the-dead-simple-step-by-step-guide-for-front-end-developers-to-getting-up-and-running-with-nodejs-express-and-mongodb)
by Christopher Buecheler  


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

```
express --view="ejs" project_name
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
