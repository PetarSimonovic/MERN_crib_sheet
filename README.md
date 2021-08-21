# MERN Crib Sheet
A crib sheet for setting up a web app using MongoDB, Express, Node and React



## Install node and NPM

Install the latest version from the [Node.js](https://nodejs.org/en/)

If there are any issues with permissions, use ```sudo chown``` to set the superuser ownership:

```
sudo chown -R $USER /usr/local/lib/node_module
```

## Set up the back-end

Set up the folder and install:

**Express:** The server framework (The E in MERN).
**Body Parser:** Responsible to get the body off of network requests.
**Nodemon:** Restarts the server when it detects changes (for a better dev experience).
**Cors:** A package that provides Connect/Express middleware that can be used to enable CORS with various options.
**Mongoose:** An elegant MongoDB object modeling for node.js

```
mkdir app-name
cd app-name
npm init -y
npm install express body-parser cors mongoose nodemon

```

Create a file called index.js that contains the following server set up:

```js

const express = require('express')
const bodyParser = require('body-parser')
const cors = require('cors')
const app = express()
const apiPort = 3000

app.use(bodyParser.urlencoded({ extended: true }))
app.use(cors())
app.use(bodyParser.json())

app.get('/', (req, res) => {
    res.send('Hello World!')
})

app.listen(apiPort, () => console.log(`Server running on port ${apiPort}`))

```

run ```node index.js```

If everything is set up properly you should see a message stating "Server running on port 3000"

Go to localhost/3000 in a browser and you should see "Hello world!"


## Set-up MongoDB

If a previous version of MongoDB is running/creating issues then stop and uninstall it:

```
$ brew services stop mongodb
$ brew uninstall mongodb
```

Otherwise:

```
$ sudo chown -R $(whoami) $(brew --prefix)/*
$ brew tap mongodb/brew
$ brew install mongodb-community
$ brew services start mongodb-community
$ ps aux | grep -v grep | grep mongod
```

Use ```mongosh``` to run mongo.

Type ```use DATABASE_NAME ``` to create a database

To connect Mongo with the server, create a directory called 'db' in the server folder, and add a file called index.js:

```
$ mkdir db
$ touch index.js
```

index.js should contain:

```js

const mongoose = require('mongoose')

mongoose
    .connect('mongodb://127.0.0.1:27017/cinema', { useNewUrlParser: true })
    .catch(e => {
        console.error('Connection error', e.message)
    })

const db = mongoose.connection

module.exports = db
```
