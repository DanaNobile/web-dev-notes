# Creating Auth Tokens with JSON Web Tokens
## Step 4: Generate and "send back" and authentication token
* This auth token is going to be something the user stores and passes along with future requests whenever it wants to do something that requires authentication
    - Like creating a post
    - Or deleting a comment

## JWT
* Lots of ways to create authentication tokens
* We will use **JSON Web Token**
* JWT is an open standard that gives us a way to securely transfer information between parties and it also tamper proof
* So we can grant certain privileges to the holder of that token

1. The server generates that token
2. The client then gets that token
3. The client can then use that token for future requests that require authentication
    * Each token is going to be associated with a specific user
    * Each token will expire after a certain amount of time

## How can we use JWT?
* [jwt documentation](https://www.npmjs.com/package/jsonwebtoken)
* We will install a library that will give us everything we need to implement JWT into our application

### Import jsonwebtoken library
`Mutation.js`

```
import bcryptjs from 'bcryptjs';
import jwt from 'jsonwebtoken'; // Add this line


// MORE CODE
```

### First - Let's show how jwt work
#### jwt syntax
```
jwt.sign({PAYLOAD}, 'SECRET');
```

* We are trying to create a token for a user and we can isolate and target that user by their unique `id`
    - We don't have to use `id`, we can put whatever we want into this object
* The `secret` is used to verify the integrity of the token (making sure that someone doesn't get a token to change the `id` to a different user)
    - The `secret` will only live on the Node.js server
    - You can make the secret anything you want
    - Later on we'll make this an `environment variable`

`Mutation.js`

```
import bcryptjs from 'bcryptjs';
import jwt from 'jsonwebtoken';

const token = jwt.sign({ id: 46 }, 'ihaveasecret');
console.log(token);

// MORE CODE
```

* Let's show our first generated token

`$ npm start`

* You will see the very long token in the Terminal

![really long jwt token](https://i.imgur.com/4S6WqRk.png)

* This is the token the server will send the client, when the client signs up or logs in and it is the same token that the client will then store and send along with future requests when it wants to be authenticated

## More about the Payload
* **note** The Payload is not meant to be encrypted and it is publicly readable
    - Anyone who has access to this token has access to this data

```
const token = jwt.sign({ id: 46 }, 'ihaveasecret');
```

* `{ id: 46 }` is the Payload

## Decode
```
import bcryptjs from 'bcryptjs';
import jwt from 'jsonwebtoken';

const token = jwt.sign({ id: 46 }, 'ihaveasecret');
console.log(`This is the token: ${token}`);

const decodedToken = jwt.decode(token);
console.log(decodedToken);

// MORE CODE
```

* This will output to the Terminal

```
This is the token: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6NDYsImlhdCI6MTU2MTc4ODI4Nn0.dON7nCbzUFCIZ7qcJKH_2a8d5ZoAieEoaf4s3Yyoobk
{ id: 46, iat: 1561788286 }
This graphql-yoga server is running
```

* We have the token but we don't have the secret but we can still see the Payload
* You see there are 2 properties
    - The id property that we provided
    - And there is also an `iat` (stands for "issued at") and this is a timestamp of when the token was initially created
        + This is a great feature built right into jwt

## Lastly, verify that this token was actually created by this server
* What's to stop someone from just creating a token on the client, spoofing the 'id' using some other user's id and then sending that token to the server?
    - The `secret` is what's stopping that from happening

### jwt.verify()
* Similar to decode, it decodes the token but it also verifies that the token was indeed created with a specific **secret**
* This is how we ensure that the tokens that we are reading are tokens that are created by this server

#### jwt.verify syntax
`jwt.verify(TOKEN_WE_WANT_TO_VERIFY, SECRET)`

* Only if the token was created with same secret will it return true, otherwise it will return false

`Mutation.js`

```
import bcryptjs from 'bcryptjs';
import jwt from 'jsonwebtoken';

const token = jwt.sign({ id: 46 }, 'ihaveasecret');
console.log(`This is the token: ${token}`);

const decodedToken = jwt.decode(token);
console.log(decodedToken);

const verifiedToken = jwt.verify(token, 'ihaveasecret');
console.log(verifiedToken);

// MORE CODE
```

* Will output to the Terminal:

```
This is the token: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6NDYsImlhdCI6MTU2MTc4ODg1MX0.BuHrJf6hBXCw3sbYpVVz0Tt26g9Vx95GCwjYyyFVPMY
{ id: 46, iat: 1561788851 }
{ id: 46, iat: 1561788851 }
This graphql-yoga server is running
```

* Above you see:
    - The token
    - The verified token
* If you pass the wrong secret you will see:

```
import bcryptjs from 'bcryptjs';
import jwt from 'jsonwebtoken';

const token = jwt.sign({ id: 46 }, 'ihaveasecret');
console.log(`This is the token: ${token}`);

const decodedToken = jwt.decode(token);
console.log(decodedToken);

const verifiedToken = jwt.verify(token, 'wrongsecret');
console.log(verifiedToken);

// MORE CODE
```

* You will see this error in the Terminal:

```
JsonWebTokenError: invalid signature
```

* It all comes down to the secret
* The client doesn't know the secret so it can't spoof the token

## What is stored in the jwt string?
* Visit this website: [jwt.io](https://jwt.io/)
* Paste in the token generated from Node.js into "Encoded"
* You will see color coded into 3 distinct pieces

1. HEADER
2. PAYLOAD
3. VERIFY SIGNATURE

* The parts are separated in our token by the "."

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6NDYsImlhdCI6MTU2MTc4ODk2NH0.2JVMqG6mNkPkP18nIht60aEMlCnThT9qmHj1du0jNQI
```

![jwt output](https://i.imgur.com/itumhrW.png)

* The HEADER just contains some information about the token in general
* We the end user will never modify this - it is just for internal purposes only, it allows it to provide information about the token type 'JWT' and it also provides information about the algorithm used to generate the signature
* We see the PAYLOAD with our user id and "issued at(iat)" timestamp value letting us know when exactly the token was created
* Lastly we have the Signature - this is what keeps things tamper proof and secure
    - The signature is nothing more that a hash
    - Both the jwt.sign() and jwt.verify() generate a signature
    - When we jwt.sign() a token it generates that token
    - The signature is added onto the end of the token
    - When we verify a token jwt.verify() we do the same thing
    - When we jwt.verify() we once again, I take the header and body data and we hash it with the secret provided
    - If I get the same signature that is shown in the token I know the token is valid since things match up
    - If I get a different signature that I know that the secret is different

## JWT is not encrypted
* It may look encrypted but it is not
* It is just base64 encoded
* Use this [base64decode.org](https://www.base64decode.org/)
* Take the Payload (purple text) from your token and paste and decode and you will see

![base64 decode](https://i.imgur.com/trQZbR0.png)

* Our HEADER and our PAYLOAD are just base64 encoded JSON

```
{"id":46,"iat":1561789960}
```

## Now let's use this in createUser inside Mutation.js

* We won't return our createUser result, instead we'll store in in a variable and await it

`Mutation.js`

```
const user = await prisma.mutation.createUser(
  {
    data: {
      ...args.data,
      password: hashedSaltedPassword,
    },
  },
  info
);
```

* Down below is where we'll return our statement
* We want to return an object that has 2 things on it

1. The user information
2. And the token (we will create that now)

`Mutation.js`

```
const Mutation = {
  async createUser(parent, args, { prisma }, info) {
    if (args.data.password.length < 8) {
      throw new Error('Password length must be at least 8 characters');
    }

    const hashedSaltedPassword = await bcryptjs.hash(args.data.password, 10);

    const user = await prisma.mutation.createUser(
      {
        data: {
          ...args.data,
          password: hashedSaltedPassword,
        },
      },
      info
    );

    return {
      user,
      token: jwt.sign({ userId: user.id }, 'ihaveasecret'),
    };
  },
```

* Currently createUser returns a type of User but we will change that so that it returns a type that has Token and User on it

`schema.graphql`

```
type AuthPayload {
  token: String!
  user: User!
}
```

* Than we update createUser

```
// MORE CODE

type Mutation {
  createUser(data: CreateUserInput!): AuthPayload!

  // MORE CODE
```

* delete this code we used for practice

```
const token = jwt.sign({ id: 46 }, 'ihaveasecret');
console.log(`This is the token: ${token}`);

const decodedToken = jwt.decode(token);
console.log(decodedToken);

const verifiedToken = jwt.verify(token, 'ihaveasecret');
console.log(verifiedToken);
```

Graphql Playground

```
mutation{
  createUser(
    data: {
      name:"mike",
      email: "mike@mike.com",
      password: "123Password"
    }
  ) {
    id
    name
    email
  }
}
```

* We add the passord field but this will not work and we'll get an error
* The reason is `createUser` is no longer returning what it was before (User) not it is return `AuthPayload`
    - What we now have access to is `user` and `token`

## Update Graphql Playground
```
mutation{
  createUser(
    data: {
      name:"mike",
      email: "mike2@mike.com",
      password: "123Password"
    }
  ) {
    user {
      id
      name
      email
    }
    token
  }
}
```

* This will also return an error!
* The problem is we are taking this selection set:

```
user {
      id
      name
      email
    }
token
```

* And we are passing it into createUser via the `info` argument

`Mutation.js`

```
// MORE CODE

const Mutation = {
  async createUser(parent, args, { prisma }, info) {
    if (args.data.password.length < 8) {
      throw new Error('Password length must be at least 8 characters');
    }

    const hashedSaltedPassword = await bcryptjs.hash(args.data.password, 10);

    const user = await prisma.mutation.createUser(
      {
        data: {
          ...args.data,
          password: hashedSaltedPassword,
        },
      },
      info
    );

// MORE CODE
```

* The problem with this is we can't select a token or a user on "what comes back"
* We can only select the "fields" from the user
* In this case when we are returning something custom like this object

```
return {
  user,
  token: jwt.sign({ userId: user.id }, 'ihaveasecret'),
};
```

* Using `info` is not what we want
* We remove it and then we can access all scaler fields

```
// MORE CODE

    const user = await prisma.mutation.createUser(
      {
        data: {
          ...args.data,
          password: hashedSaltedPassword,
        },
      },// REMOVE info
    );

// MORE CODE
```

* Remember - when we leave off the second argument
    - When we do this it will return all scaler fields
    - So for the user it will return any of the scaler fields we have listed over in our type definition

`schema.graphql`

```
type User {
  id: ID!
  name: String!
  email: String!
  password: String!
  posts: [Post!]!
  comments: [Comment!]!
}
```

* The scaler values are:
    - id
    - name
    - email
    - password (we will lock this down later!)

## Save and test
```
mutation{
  createUser(
    data: {
      name:"mike",
      email: "mike2@mike.com",
      password: "123Password"
    }
  ) {
    user {
      id
      name
      email
    }
    token
  }
}
```

* Will finally work and output something like:

```
{
  "data": {
    "createUser": {
      "user": {
        "id": "cjxi895g200070701btxjz0gs",
        "name": "mike",
        "email": "mike2@mike.com"
      },
      "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiJjanhpODk1ZzIwMDA3MDcwMWJ0eGp6MGdzIiwiaWF0IjoxNTYxODU1MTkzfQ.lTcTZFc6TdnmLxIyp-1ZL-J_1Nk1Cz5ykiZXCnlBq7E"
    }
  }
}
```

* We have our user
* And we have our authentication token that the user can store and use later to perform "privileged operations" like creating a post or updating a comment
* We've moved all of those user fields onto a user property and we added the addition of a token
* View it in the DB

## Next
* We know how to generate a token
* Next we'll verify a token and use it to determine whether or not someone can do something
