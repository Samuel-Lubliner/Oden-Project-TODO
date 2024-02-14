# Oden-Project-TODO

TODO list made with JavaScript and [localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API/Using_the_Web_Storage_API)

Bundled with [webpack](https://webpack.js.org/).

run `npm init` in your project directory to generate a package.json file.

`npm install webpack webpack-cli --save-dev` to install webpack to the node_modules directory of your project.

To bundle the lodash dependency with index.js, we'll need to install the library locally:

`npm install --save lodash`

configuration file. This is much more efficient than having to manually type in a lot of commands in the terminal

`npx webpack --config webpack.config.js`

If a webpack.config.js is present, the webpack command picks it up by default. We use the --config option here only to show that you can pass a configuration of any name. This will be useful for more complex configurations that need to be split into multiple files.

adjust our package.json by adding an npm script:

package.json

```json
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack"
   },
```

Now the `npm run build` command can be used in place of the npx command we used earlier. Note that within scripts we can reference locally installed npm packages by name the same way we did with npx. This convention is the standard in most npm-based projects because it allows all contributors to use the same set of common scripts.

In order to import a CSS file from within a JavaScript module, you need to install and add the style-loader and css-loader to your module configuration:

`npm install --save-dev style-loader css-loader`

Load images like backgrounds and icons using the built-in Asset Modules.

TODO:

- Note that webpack will not alter any code other than import and export statements. If you are using other ES2015 features, make sure to use a transpiler such as Babel via webpack's loader system.
