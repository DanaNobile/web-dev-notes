# Virtual Fields, Login/Logout middleware and Protecting Routes
1. We will wire up our **Logout** button
2. We will wire up the **Login** button
3. Create Avatar
4. When visiting `/add` route, we want to make sure user is logged in

## Where did our Login/Register buttons go?
* We are now logged in and our `layout.pug` has a conditonal statement

`layout.pug`

```
if user

else
  li.nav__item: a.nav__link(href="/register", class=(currentPath.startsWith('/register') ? 'nav__link--active' : '')) Register
  li.nav__item: a.nav__link(href="/login", class=(currentPath.startsWith('/login') ? 'nav__link--active' : '')) Log In
```

* And when we are logged in `if user`, we show nothing
* Let's fix that by adding this:

```
if user
  li.nav__item: a.nav__link(href="/logout", class=(currentPath.startsWith('/logout') ? 'nav__link--active' : ''))
    != h.icon('logout')
    span Logout
else
  li.nav__item: a.nav__link(href="/register", class=(currentPath.startsWith('/register') ? 'nav__link--active' : '')) Register
  li.nav__item: a.nav__link(href="/login", class=(currentPath.startsWith('/login') ? 'nav__link--active' : '')) Log In
```

* Let's grab the logout.svg using Node.js
* Update the code inside `/get-icons.js` to this:

`get-icons.js`

```js
const fileSystem = require('fs');
// important to look at URL and use http or https and require it
const https = require('https');

// browse to this URL out and see what it looks like
const pencilIconUrl =
  'https://raw.githubusercontent.com/wesbos/Learn-Node/master/stepped-solutions/45%20-%20Finished%20App/public/images/icons/logout.svg';
const pencilIcon = fileSystem.createWriteStream(
  __dirname + '/public/images/icons/logout.svg'
);
const request = https.get(pencilIconUrl, function(response) {
  response.pipe(pencilIcon);
});
```

* Run that code by navigating to the project root of our app and typing this command
* `$ node get-icons.js`
* Now view inside `/public/images/icons/` and you should see you just added `logout.svg`

#### Update our _nav.scss
```
.nav {
  display: flex;
  list-style: none;
  margin: 0;
  padding: 0;
  justify-content: space-between;
  background: $black;
  &__section {
    display: flex;
    &--search {
      flex: 1 1 auto;
    }
  }
  &__item {
    display: flex;
  }
  &__link {
    background: $black;
    color: white;
    border-right: 1px solid rgba(255, 255, 255, 0.1);
    text-transform: uppercase;
    padding: 1.2rem 2rem 1rem 2rem;
    display: block;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    transition: transform 0.2s;
    border-bottom: 5px solid transparent;
    svg {
      width: 40px;
      transition: all 0.2s;
      fill: white;
      margin-bottom: 1rem;
    }
    &:hover,
    &--active {
      border-bottom-color: rgba(0, 0, 0, 0.2);
      border-right-color: rgba(0, 0, 0, 0.05);
      svg {
        transform: scale(1.2);
      }
    }
  }
}
// more code
```

* View the home page

![home page with logout svg](https://i.imgur.com/ZDxZScp.png)

* The icon is now white
* The icon has a cool grow/shrink animation on it (hover over logout to see animation)
* The other nav items are different sizes but if they all have icons it will look better
* We now need to wire up our ability to log out

## Logout
`authController.js`

```
exports.logout = (req, res) => {
  req.logout();
  req.flash('success', 'You are now logged out');
  res.redirect('/');
};
```

* Call the `logout()` methed
* Flash success message
* Redirect to home page `/`

### Update `/logout` route
* When the user clicks on the logout button now, we get a `404`
* We just need to update the route to point to our `logout()` controller

`index.js`

```js
// more code
router.post(
  '/register',
  userController.validateRegister,
  userController.register,
  authController.login
);
router.get('/logout', authController.logout);
// more code
```

## Test it out
1. Save
2. Click Logout button
3. You should be redirected to `/` with success flash message

* We just do a simple `if/else`
* If there is a `user` show one thing, else (not a `user`) show something else

## Login ---> Time to add the POST route handler
* If we try to login we get a `404`
* We have the `get` `/login` route working
* Next, we have to set up the `POST` `/login` route
* This is an easy task because we already built `authController.login` so we can just resuse it

### Add our route
`index.js`

```
router.get('/login', userController.loginForm);
router.post('/login', authController.login); // add this line
router.get('/register', userController.registerForm);
```

* Try to log in again and it should log you in
* Logout and log in
* Yes! Victory!
* When we log in with bad credentials we just see `unauthorized`
  - We'll need to fix this later

## Add our avatar
![gravatar](https://i.imgur.com/wgCG9De.png)

`layout.pug`

```
.nav__section.nav__section--user
    if user
      li.nav__item: a.nav__link(href="/logout", class=(currentPath.startsWith('/logout') ? 'nav__link--active' : ''))
        != h.icon('logout')
        span Logout
      li.nav__item: a.nav__link(href="/account", class=(currentPath.startsWith('/account') ? 'nav__link--active' : ''))
        img.avatar(src=user.gravatar + 'd=retro')
    else
      li.nav__item: a.nav__link(href="/register", class=(currentPath.startsWith('/register') ? 'nav__link--active' : '')) Register
      li.nav__item: a.nav__link(href="/login", class=(currentPath.startsWith('/login') ? 'nav__link--active' : '')) Log In
```

* `gravatar` is a globally recognized avatar
* Every time you have a user's **email** address, you can grab their `gravatar` from it
* We won't build something to enable users to upload their own avatar
* We will just use the built in gravatar service which will show us their globally recognized avatar

## Virtual Fields
* Do we need to add an `avatar` field inside our User model (_User.j_s)?
    - No. We will add this using a `virtual field`
* A virtual field in **Mongoose** can be generated
  - Rather than storing all the data about a user
  - Sometimes your fields can be generated
    + If someone's average running per day you wouldn't store it in mile and kilometers
      * you would just store one and generate the other one on the fly

## Add a virtual field
`User.js`

```js
// more code

// add this chunk of code
userSchema.virtual('gravatar').get(function() {
  return `https://shop.spreadshirt.co.uk/image-server/v1/designs/2036216,width=178,height=178/george-best.png`;
});

userSchema.plugin(passportLocalMongoose, { usernameField: 'email' });
// more code
```

* Also make this adjustment

`layout.pug`

Change this:

```
img.avatar(src=user.gravatar + 'd=retro')
```

To this:

```
img.avatar(src=user.gravatar)
```

### View in browser
You'll see George Best photo in top right

![george best gravatar](https://i.imgur.com/TMMOja6.png)

* George Best is not in our database
  - He's just virtual
  - He is being pulled up in a virtual field called `gravatar`

### Adding a Gravatar and md5
* Gravatars use a hashing algorithm called `md5`
* `md5` just takes the user's email address and hashes it

#### Make sure to import md5
`User.js`

```
const mongoose = require('mongoose');
const Schema = mongoose.Schema;
const validator = require('validator');
const md5 = require('md5'); // add this line
// more code
```

### Update our gravatar code
`User.js`

```
// more code
userSchema.virtual('gravatar').get(function() {
  const hash = md5(this.email);
  return `https://gravatar.com/avatar/${hash}?s=75`;
});
// more code
```

* We use a proper ES5 function because we need to use `this` inside it
* We hash the user's email
* `s` is size
* We point to the gravatar site and pass our **hash** email
* Update `layout.pug` to return it to this code:

`img.avatar(src=user.gravatar + 'd=retro')`

### View in browser
You'll see your user's gravatar (_if they have one and just default `G` if they don't_)

![my md5 gravatar hash](https://i.imgur.com/XrE6jbJ.png)

![my gravatar](https://i.imgur.com/xqMz3lu.png)

* You can take the url and use it as an image

`https://gravatar.com/avatar/39bc26d9d735a3e215473f085cd22345?s=200d=retro`

* Change the size to make it larger

## Houston we have a problem
* When you log out you should not be able to go to the `/add` route
* `/add` is a private page and should only be visible if you are logged in

### Add middleware to check if the user is logged in
`authController.js`

```
// more code
exports.logout = (req, res) => {
  req.logout();
  req.flash('success', 'You are now logged out');
  res.redirect('/');
};

// add this code below
exports.isLoggedIn = (req, res, next) => {
  // first check if the user is authenticated
  if (req.isAuthenticated()) {
    next(); // all good. user is logged in
    return;
  }
  req.flash('error', 'You must be logged in to perform that action');
  res.redirect('/login');
};
```

* `isAuthenticated()` is a method that comes with **PassportJS**
  - This makes checking authentication easy and one of the perks of using `PassportJS`
  - `PassportJS` gives us this and many other methods we can easily access
  - If they are authenticated we move onto the `next` **middleware**
  - If they are not we `flash` them an **error** and redirect them to `/login`

## Add isLoggedIn to our `/add` route
`index.js`

```js
// more code
router.get('/store/:slug', catchErrors(storeController.getStoreBySlug));
router.get('/add', authController.isLoggedIn, storeController.addStore);
// more code
```

## Try it out
1. Log in and visit `/add`
2. Log out
3. Try to visit `/add` and you should be redirected to `/login`

### Houston We have a problem
After logging out, we click on `/add` and get redirected to `/login` but if we then click on `/stores` we get a delayed flash message

* We'll deal with this later
