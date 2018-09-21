Setup vue.js client for the front-end:
npm install -g @vue/cli

Start a project with:
vue init <template-name> <project-name>

To see what templates can be used:
vue list

In the command line:
vue init webpack client

Then we get a run-through.
Enter through them, say 'yes' for view router.
ESLint 'yes'.
ESLint 'Standard'.
Setup e2e tests with Nightwatch 'yes'.

When done cd into 'client'.
Then to install all the package dependencies:
npm install

We can view our app with:
npm run dev

It will give an address to view the application.

Commit and push to github.

Create a new directory 'server' in the same directory as 'client'.
cd into it.

Then create a new package.json file (-f is force):
npm init -f

Then we want to install nodemon and eslint:
npm install --save nodemon eslint

Init eslint:
node ./node_modules/eslint/bin/eslint.js --init
Then 'use a popular styles guide'
Select 'standard'.
Select 'javascript'.

Then to use them change the package.json scripts:

"scripts": {
    "start": "nodemon src/app.js --exec  \"npm run lint && node\"",
    "lint": "eslint src/**/*.js"
  }

Create a new folder 'src'.
Inside that create 'app.js'.

To test, in app.js:
console.log('hello')

Then run:
npm start

This will be an express app so we need to install some more dependencies:
npm install --save express body-parser cors morgan

In app.js require in the packages:

const express = require('express')
const bodyParser = require('body-parser')
const cors = require('cors')
const morgan = require('morgan')

Build an express server:
const app = express()

Use the log generator 'morgan':
app.use(morgan('combined'))

Allow the app to easily parse json requests:
app.use(bodyParser.json())

Allow any host or client to access (make sure you have good security):
app.use(cors())

Setup an end point that when hit will send back a message:
app.get('/status', (req, res) => {
  res.send({
    message: 'hello world'
  })
})

Setup a port to listen on:
app.listen(process.env.PORT || 8081)

Now if we start the server and go to 'localhost:8081/status' we should see the message object.
In the command line we should see the morgan logs.

Create a .gitignore file to prevent 1000's of node modules being uploaded:
client/node_modules/
server/node_modules/

Then add, commit and push these changes.

First step of functionality is to create a registry page.
cd into the client folder.
Install 'axios' which is a library that uses http requests to access the backend:
npm install --save axios 

Create a 'services' folder inside of 'src' inside of 'client'.
Inside of that create 'Api.js' file.
This file will be used as a connector to the backend.
Import axios.
Then export the connector.
The axios object has a function that points to our backend api.
Then we can use this to access our endpoints:

import axios from 'axios'

export default () => {
  return axios.create({
    baseURL: 'https://localhost:8081/'
  })
}

Create 'AuthenticationService.js' in the same folder.
This will be used for hitting the register endpoint (that we are yet to build).
Import/create the Api object that we import from Api.js which is the axios object.
Then we export a method with a couple of methods.
Create a 'register' method with a 'credentials' parameter.
We use the Api axious module to post request to the register endpoint on the express server and pass in the credentials.

import Api from '@/services/Api'

export default {
  register (credentials) {
    return Api().post('register', credentials)
  }
}

Now we can go to the backend and setup that endpoint.
In app.js delete the test route.
Replace it with (note the ` instead of '):

app.post('/register', (req, res) => {
  res.send({
    message: `User ${req.body.email} was registered.`
  })
})

Now we can test this with postman.
Have front and backend servers running.
Enter this url as a post request: http://localhost:8081/register
Click on 'body' then 'raw' then change to JSON then type in:

{
	"email": "email@email.com",
	"password": "1234"
}

Hit send and you should get back:

{
    "message": "User email@email.com was registered."
}

Now let's create a UI for registering.
Open up index.js inside of 'router' inside of 'src' inside of 'client'.
This shows us the different routes.
We want to make one for register.
Import the 'Register' component (that we haven't made yet).
Then create the register path:

import Vue from 'vue'
import Router from 'vue-router'
import HelloWorld from '@/components/HelloWorld'
import Register from '@/components/Register'

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      name: 'HelloWorld',
      component: HelloWorld
    },
    {
      path: '/register',
      name: 'register',
      component: Register
    }
  ]
})

So copy the 'HelloWorld.vue' inside of components and paste a new one called 'Register.vue'.
Then hollow out the contents so all that remains:

<template>
  <div>
    <h1>Register</h1>
  </div>
</template>

<script>
export default {
  data () {
    return {
    }
  }
}
</script>

<style scoped>

</style>

Then in App.vue comment out the img as it will still appear on our register route.

Now we need to add some inputs.
We need email, password, and a button.
So under the h1 element add in:

<input type="email" name="email" placeholder="email" />
<br>
<input type="password" name="password" placeholder="password" />
<br>
<button>Register</button>

Now we can combine the html to the controller.
Everything in the script tag is the controller.
Add 'v-model=""' to the inputs.
This tells view to look for any data with that name and bind it:

<input type="email" name="email" v-model="email" placeholder="email" />
<input type="password" name="password" v-model="password" placeholder="password" />

Then in the script we can set those values to an empty string:

export default {
  data () {
    return {
      email: '',
      password: ''
    }
  }
}

Then we add a listener to the button:

<button
  @click="register">Register</button>

When it is clicked we want to run a register method.
In the script create methods and register:

<script>
export default {
  data () {
    return {
      email: '',
      password: ''
    }
  },
  methods: {
    register () {
      console.log('register button was clicked', this.email, this.password)
  }
}
</script>

Now when we enter an email and password the console will log those values.
Let's pass these into the AuthenticationService.
In the script import it in.
Then add it to the register as an async function:

<script>
import AuthenticationService from '@/services/AuthenticationService'
export default {
  data () {
    return {
      email: '',
      password: ''
    }
  },
  methods: {
    async register () {
      const response = await AuthenticationService.register({
        email: this.email,
        password: this.password
      })
      console.log(response.data)
  }
}
</script>

