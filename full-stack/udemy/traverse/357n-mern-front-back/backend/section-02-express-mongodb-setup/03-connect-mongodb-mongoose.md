# Connecting to MongoDB with Mongoose
* We are going to use the `config` package we installed earlier

`$ mkdir config/default.json`

* In mongo atlas
    - connect > connect to your application
    - Copy URI and paste into `default.json`

`default.json`

```
{
  "mongoURI": "mongodb+srv://philadmin:<password>@devconnector-a2gjt.mongodb.net/<dbname>?retryWrites=true&w=majority"
}
```

* We could put our connection logic directly inside our `server.js` but we want to keep that file clean
* We'll create `config/db.js`

`config/db.js`

```
const mongoose = require('mongoose');
const config = require('config');

const db = config.get('mongoURI');

mongoose.connect(db);
```

* `mongoose.connect()` returns a Promise and we could use `then()` but we will use async/await
    - Makes code look cleaner
    - Makes code look synchronous even though it is asynchronous
    - We'll use a `try/catch` block to let us know if our connection doesn't work
    - You must use `async` and `await` together
    - Since `mongoose.connect()` returns a Promise you must put `await` before it

`config/db.js`

```

const mongoose = require('mongoose');
const config = require('config');

const db = config.get('mongoURI');

const connectDB = async () => {
  try {
    await mongoose.connect(db);

    console.log('MongoDB connected...');
  } catch (err) {
    console.error(err.message);
    // we want to exit from the process with failure
    process.exit(1);
  }
};

module.exports = connectDB;
```

* Connect our Database to our server

`server.js`

```
const express = require('express');
const connectDB = require('./config/db');

const app = express();

// Connect Database
connectDB();

// MORE CODE
```

## Add dotenv
* This will protect our secret info from github
* A better solution than `config/default.json`

`$ npm i dotenv`

`config/config.env`

* Put in your db name (change `<dbname>` to `devconnector`)
* Put in your password

```
MONGO_URI=mongodb+srv://philadmin:<password>@devconnector-a2gjt.mongodb.net/<dbname>?retryWrites=true&w=majority
```

* Update `.gitignore`

`.gitignore`

* We don't want to see our environment variables on github

```
node_modules/
.DS_Store
config/config.env
.env
```

`server.js`

* And point to our environment variables

```
const express = require('express');
const dotenv = require('dotenv').config({ path: './config/config.env' });

// MORE CODE
```

## Add npm colors
`$ npm i colors`

`server.js`

```
const express = require('express');
const colors = require('colors'); // eslint-disable-line no-unused-vars
const dotenv = require('dotenv').config({ path: './config/config.env' });

// MORE CODE
```

### And use colors to show yellow for server message

`server.js`

```
// MORE CODE

  console.log(
    `Server running in ${process.env.NODE_ENV} mode on port ${PORT}`.yellow.bold
  )
);
```

### And update our Database to show colors
`config/db.js`

* We get rid of all Mongoose warnings with the object we pass as a second argument

```
const mongoose = require('mongoose');

const connectDB = async () => {
  try {
    const conn = await mongoose.connect(process.env.MONGO_URI, {
      useNewUrlParser: true,
      useCreateIndex: true,
      useFindAndModify: false,
      useUnifiedTopology: true,
    });

    // show developer we are connected to MongoDB
    console.log(
      `MongoDB Connected: ${conn.connection.host}`.cyan.underline.bold
    );
  } catch (err) {
    console.error(err.message);
    // we want to exit from the process with failure
    process.exit(1);
  }
};

module.exports = connectDB;
```

## Add NODE_ENV to config/config.env
`config.env`

```
// MORE CODE

NODE_ENV=Development

// MORE CODE
```

## Run app
`$ npm run dev`

* You should run server and connect to Database

![server and mongodb connected](https://i.imgur.com/CXWwngv.png)


