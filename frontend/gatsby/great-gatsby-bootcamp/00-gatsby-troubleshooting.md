# Troubshooting:
* [react-fire-dom is not detected](https://i.imgur.com/n6q5txs.png)
* https://github.com/gatsbyjs/gatsby/issues/11934
* replace `react-dom` with `@hot-loader/react-dom` to remove this warning

`$ npm uninstall react-dom`

`$ npm i -D @hot-loader/react-dom`

* Create `gatsby-node.js` in root of gatsby project

`gatsby-node.js`

```
exports.onCreateWebpackConfig = ({ getConfig, stage }) => {
  const config = getConfig()
  if (stage.startsWith('develop') && config.resolve) {
    config.resolve.alias = {
      ...config.resolve.alias,
      'react-dom': '@hot-loader/react-dom'
    }
  }
}
```

* Above solution works

## Vim write files
* Sometimes you are readonly and your changes are not recorded
* `$ w!` will force a write
* Example of a problem, i was updating my siteMetadata and didn't see it in the GraphQL Playground and had to write to it with `w!` to make the changes stick