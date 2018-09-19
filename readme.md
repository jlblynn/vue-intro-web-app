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

