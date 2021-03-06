# Heroku Environment Variables
* None of our environment variables run in `production`
* In `production` we have to use the heroku CLI
    - It will enable us to set up process variables on env
        + `process.env`
    - `process.env.NODE_ENV` is set to 'production' (on Heroku servers)
    - But for these environment variables:

`firebase.js`

```
const config = {
  apiKey: process.env.FIREBASE_API_KEY,
  authDomain: process.env.FIREBASE_AUTH_DOMAIN,
  databaseURL: process.env.FIREBASE_DATABASE_URL,
  projectId: process.env.FIREBASE_PROJECT_ID,
  storageBucket: process.env.FIREBASE_STORAGE_BUCKET,
  messagingSenderId: process.env.FIREBASE_MESSAGING_SENDER_ID,
};
```

* **IMPORTANT!** We'll have to set them up manually
  - Otherwise when we try to use them here:

`webpack.config.js`

```
// MORE CODE
plugins: [
  CSSExtract,
  new webpack.DefinePlugin({
    'process.env.FIREBASE_API_KEY': JSON.stringify(
      process.env.FIREBASE_API_KEY
    ),
    'process.env.FIREBASE_AUTH_DOMAIN': JSON.stringify(
      process.env.FIREBASE_AUTH_DOMAIN
    ),
    'process.env.FIREBASE_DATABASE_URL': JSON.stringify(
      process.env.FIREBASE_DATABASE_URL
    ),
    'process.env.FIREBASE_PROJECT_ID': JSON.stringify(
      process.env.FIREBASE_PROJECT_ID
    ),
    'process.env.FIREBASE_STORAGE_BUCKET': JSON.stringify(
      process.env.FIREBASE_STORAGE_BUCKET
    ),
    'process.env.FIREBASE_MESSAGING_SENDER_ID': JSON.stringify(
      process.env.FIREBASE_MESSAGING_SENDER_ID
    ),
  }),
],
// MORE CODE
```

* And we will pass invalid values to the client side JavaScript when Heroku runs Webpack
  - We just need to add all the stuff from `.env.development` and add it into a command
  - Shut down test suite
  - Clear screen with `$ clear`

### Heroku CLI
* `$ heroku config`
    - Prints out a list of all your environment variables
* Syntax
    - `$ heroku config:set KEY=value`
* Remove with
    - `$ heroku config:unset KEY`

### Add them all in one shot
* Separate them with a space
* **tip** Do this in a separate text editor and then paste them into the Terminal

```
$ heroku config:set FIREBASE_API_KEY=AIzaSyDYG7aalHm2evdm0-bqfV_9QJNAmhwtuaI FIREBASE_AUTH_DOMAIN=expensify-ca9db.firebaseapp.com FIREBASE_DATABASE_URL=https://expensify-ca9db.firebaseio.com FIREBASE_PROJECT_ID=expensify-ca9db FIREBASE_STORAGE_BUCKET=expensify-ca9db.appspot.com FIREBASE_MESSAGING_SENDER_ID=447434791238
```

* It will set all 6 environment variables and set up the app

## ignore secret files
* Adding
    - `.env.test`
    - `.env.development`

```
node_modules
*.swp
*js.swp
*~
public/dist/
.env.test
.env.development
```

### Git stuff
`$ git add .`

`$ git commit -m 'Setup test database env`

`$ git push`

`$ git push heroku master` 

`$ heroke open`

* Make sure you can see your app in the browser with a Heroku URL
* Create an expense
* Make sure you see it inside your production database

## Check if dev database is working too
* This should work on same DB (production)
* **note** `process.env` is not native to the browser
