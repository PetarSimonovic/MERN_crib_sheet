# MERN Crib Sheet
A crib sheet for setting up a web app using MongoDB, Express, Node and React



## Install node and NPM

Install the latest version from the [Node.js](https://nodejs.org/en/)

If there are any issues with permissions, use ```sudo chown``` to set the superuser ownership:

```
sudo chown -R $USER /usr/local/lib/node_module
```

## Set up the back-end

### Installation

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


### Set-up MongoDB

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

**db/index.js** should contain:

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

**server/index.js*** needs to be updated to make the connection. It should look like this:

```js

const express = require('express')
const bodyParser = require('body-parser')
const cors = require('cors')

const db = require('./db')

const app = express()
const apiPort = 3000

app.use(bodyParser.urlencoded({ extended: true }))
app.use(cors())
app.use(bodyParser.json())

db.on('error', console.error.bind(console, 'MongoDB connection error:'))

app.get('/', (req, res) => {
    res.send('Hello World!')
})

app.listen(apiPort, () => console.log(`Server running on port ${apiPort}`))

```


run ```nodemon index.js``` to check everything is working (use ```nodemon index.js``` rather than ```node index.js``` as nodemon will monitor changes and update)

### Create models

In the server folder, create a 'models' directory that will store the schema for the database.

In 'models' create a .js file, and give it an appropriate name for the project. 

Eg, the file for a movie database could be called 'movie-model.js' and contain the following:

```js
const mongoose = require('mongoose')
const Schema = mongoose.Schema

const Movie = new Schema(
    {
        name: { type: String, required: true },
        time: { type: [String], required: true },
        rating: { type: Number, required: true },
    },
    { timestamps: true },
)

module.exports = mongoose.model('movies', Movie)
```
### Routes and controllers

In the server folder create two more directories: 'routes' and 'controllers'.

In 'routes' add a file called 'PROJECT_NAME-router.js'

In 'controllers' add a file called 'PROJECT_NAME-ctrl.js'

The controller file below contains the CRUD code for a movie project:

```js
const Movie = require('../models/movie-model')

createMovie = (req, res) => {
    const body = req.body

    if (!body) {
        return res.status(400).json({
            success: false,
            error: 'You must provide a movie',
        })
    }

    const movie = new Movie(body)

    if (!movie) {
        return res.status(400).json({ success: false, error: err })
    }

    movie
        .save()
        .then(() => {
            return res.status(201).json({
                success: true,
                id: movie._id,
                message: 'Movie created!',
            })
        })
        .catch(error => {
            return res.status(400).json({
                error,
                message: 'Movie not created!',
            })
        })
}

updateMovie = async (req, res) => {
    const body = req.body

    if (!body) {
        return res.status(400).json({
            success: false,
            error: 'You must provide a body to update',
        })
    }

    Movie.findOne({ _id: req.params.id }, (err, movie) => {
        if (err) {
            return res.status(404).json({
                err,
                message: 'Movie not found!',
            })
        }
        movie.name = body.name
        movie.time = body.time
        movie.rating = body.rating
        movie
            .save()
            .then(() => {
                return res.status(200).json({
                    success: true,
                    id: movie._id,
                    message: 'Movie updated!',
                })
            })
            .catch(error => {
                return res.status(404).json({
                    error,
                    message: 'Movie not updated!',
                })
            })
    })
}

deleteMovie = async (req, res) => {
    await Movie.findOneAndDelete({ _id: req.params.id }, (err, movie) => {
        if (err) {
            return res.status(400).json({ success: false, error: err })
        }

        if (!movie) {
            return res
                .status(404)
                .json({ success: false, error: `Movie not found` })
        }

        return res.status(200).json({ success: true, data: movie })
    }).catch(err => console.log(err))
}

getMovieById = async (req, res) => {
    await Movie.findOne({ _id: req.params.id }, (err, movie) => {
        if (err) {
            return res.status(400).json({ success: false, error: err })
        }

        if (!movie) {
            return res
                .status(404)
                .json({ success: false, error: `Movie not found` })
        }
        return res.status(200).json({ success: true, data: movie })
    }).catch(err => console.log(err))
}

getMovies = async (req, res) => {
    await Movie.find({}, (err, movies) => {
        if (err) {
            return res.status(400).json({ success: false, error: err })
        }
        if (!movies.length) {
            return res
                .status(404)
                .json({ success: false, error: `Movie not found` })
        }
        return res.status(200).json({ success: true, data: movies })
    }).catch(err => console.log(err))
}

module.exports = {
    createMovie,
    updateMovie,
    deleteMovie,
    getMovies,
    getMovieById,
}

```

the routes in the router file would be as follows:

```js
const express = require('express')

const MovieCtrl = require('../controllers/movie-ctrl')

const router = express.Router()

router.post('/movie', MovieCtrl.createMovie)
router.put('/movie/:id', MovieCtrl.updateMovie)
router.delete('/movie/:id', MovieCtrl.deleteMovie)
router.get('/movie/:id', MovieCtrl.getMovieById)
router.get('/movies', MovieCtrl.getMovies)

module.exports = router

```

Add the router to the  **server/index.js** file - it should look like this:

```js
const express = require('express')
const bodyParser = require('body-parser')
const cors = require('cors')

const db = require('./db')
const movieRouter = require('./routes/movie-router')

const app = express()
const apiPort = 3000

app.use(bodyParser.urlencoded({ extended: true }))
app.use(cors())
app.use(bodyParser.json())

db.on('error', console.error.bind(console, 'MongoDB connection error:'))

app.get('/', (req, res) => {
    res.send('Hello World!')
})

app.use('/api', movieRouter)
```

### Test the app

download and install Postman (https://www.postman.com/downloads/) and Robo3T (https://robomongo.org/). 

These are desktop applications that can test routes and MongoDB

## Set up the front-end

### Set up React


Rerturn to the root directory and set up react in a new folder called 'client'

```
$ npx create-react-app client

```

Once installed, make sure that react is on a different port to the back-end (ie, in example above the port is 3000 for the backend)

Run ```yarn start``` in the client folder and the React default page should load.

Delete the React default files using ```rm App.css index.css App.test.js serviceWorker.js```

Install the following dependencies:

**axios:**  A Javascript library used to make HTTP requests from node.js or XMLHttpRequests from the browser

**bootstrap:** Open-source toolkit and front-end component library  for developing with HTML, CSS, and JS.

**styled-components:** It allows you to write actual CSS code to style your components.

**react-table:** A lightweight, fast, and extendable data grid built for React.

**react-router-dom:** DOM bindings for React Routers.

```yarn add styled-components react-table react-router-dom axios bootstrap```

In the src folder create directories that will manage the various front-end aspects of the app. 



```
$ cd src
$ mkdir api app components pages style
```

Each folder also needs an index.js file. Move the App.js file to the app directory and rename it to 'index.js', then create the others:

```
$ touch api/index.js components/index.js pages/index.js style/index.js
```

### Update src/index.js

**src/index.js**

```js
import React from 'react'
import ReactDOM from 'react-dom'
import App from './app'

ReactDOM.render(<App />, document.getElementById('root'))
```

### Create components

Create jsx files in the components folder

```
$ touch components/NavBar.jsx components/Logo.jsx components/Links.jsx
```

Use the following as examples for the styled up components (note: the backticks are necessary for the style variables)

**logo.jsx** example

```js
import React, { Component } from 'react'
import styled from 'styled-components'

import logo from '../logo.svg'

const Wrapper = styled.a.attrs({
    className: 'navbar-brand',
})``

class Logo extends Component {
    render() {
        return (
            <Wrapper href="https://example.com">
                <img src={logo} width="50" height="50" alt="sambarros.com" />
            </Wrapper>
        )
    }
}

export default Logo
```

**links.jsx**

```js
import React, { Component } from 'react'
import { Link } from 'react-router-dom'
import styled from 'styled-components'

const Collapse = styled.div.attrs({
    className: 'collpase navbar-collapse',
})``

const List = styled.div.attrs({
    className: 'navbar-nav mr-auto',
})``

const Item = styled.div.attrs({
    className: 'collpase navbar-collapse',
})``

class Links extends Component {
    render() {
        return (
            <React.Fragment>
                <Link to="/" className="navbar-brand">
                    My first MERN Application
                </Link>
                <Collapse>
                    <List>
                        <Item>
                            <Link to="/movies/list" className="nav-link">
                                List Movies
                            </Link>
                        </Item>
                        <Item>
                            <Link to="/movies/create" className="nav-link">
                                Create Movie
                            </Link>
                        </Item>
                    </List>
                </Collapse>
            </React.Fragment>
        )
    }
}

export default Links
```

**navbar.jsx**

```js
import React, { Component } from 'react'
import styled from 'styled-components'

import Logo from './Logo'
import Links from './Links'

const Container = styled.div.attrs({
    className: 'container',
})``

const Nav = styled.nav.attrs({
    className: 'navbar navbar-expand-lg navbar-dark bg-dark',
})`
    margin-bottom: 20 px;
`

class NavBar extends Component {
    render() {
        return (
            <Container>
                <Nav>
                    <Logo />
                    <Links />
                </Nav>
            </Container>
        )
    }
}

export default NavBar
```


