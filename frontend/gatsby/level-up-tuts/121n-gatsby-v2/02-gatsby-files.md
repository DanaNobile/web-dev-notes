# Gatsby Files
## .cache/
`.cache` used for performance

* Sometimes you need to delete this as it will be recreated at run time
* It is what makes Gatsby so darn fast

## node_modules

## public/
* Folder generated by gatsby

## src/
* Everything we build in gatsby will be in this folder

`components`

* header.js
* layout.css
* layout.js

### layouts/
* This folder was in V1 and is not in V2 and the layout automatically wrapped arround your components
* We now have in V2 a layout.js and header.js which are now just components
* We have a layout.css which will layout out our base styles

## pages/
* 404.js
* index.js
    - This imports our `Layout` from `layout.js` which has the `Header` component inside it
* We now need to wrap the `Layout` component around each individual page

## Why this layout.js change from V1 to V2?
* V2 gives us more flexability

## Routing in Gatsby
* Pages are routes and the URL will be the page file name
    - /pages/page-2.js will have a route of `/page-2`

## images/
* Should be self explanatory

## .gitignore
* Should be self explanatory

## .prettierrc
* Makes your code editor format your code
* Gatsby by default turns semi-colons off (turn on with `"semi": true,`)

## gatsby-browser.js
* APIs

## gastby-config.js
* Site meta data
* Allows you to store some variables that you can query off of in GraphQL
* plugins
    - Gatsby has lots of plugins and this is where you can easily extend Gatsby with these plugins
        + Out of box Gatsby comes with:
            * `gatsby-plugin-react-helmet`
                - Allows you to have custom meta data per component
                - Was created by the NFL
            * `gatsby-plugin-offline`
                - Has to do with progressive web apps
                - It institutes a services worker to allow this application to work well offline
            * `gatsby-plugin-manifest`
                - Contains our basic manifest information for our progressive web apps

## gatsby-node.js
* This is where we go to create pages in the build process
* Example: blog
    - Your blog brings in content from WordPress instead of creating an individual file inside of pages/ you can go through, hit your API, and create pages with a template with results from the API

## gatsby-ssr.js
* Renders Gatsby's Server Side Rendering APIs

## LICENSE
* Have your lawyers read this - basic MIT License

## package-lock.json
* Locks in all our package versions so that i or anyone on my team use the site when we install the packages we will all get the exact same version of the software installed rather then versions within a parameter

## package.json
* All our package info
* Our dependencies

## README.md
* Basic README for Gatsby
* Delete all the content and create your own README

## yarn.lock
* Delete this as we'll use `npm` in this project
