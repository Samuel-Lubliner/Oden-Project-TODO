# Oden-Project-TODO

TODO list made with JavaScript and [localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API/Using_the_Web_Storage_API)

Bundled with [webpack](https://webpack.js.org/).

run `npm init` in your project directory to generate a package.json file.

`npm install webpack webpack-cli --save-dev` to install webpack to the node_modules directory of your project.

Adjust package.json file by marking our package as private, as well as removing the main entry. This is to prevent an accidental publish of your code.

## Why webpack?

Lets say we have the following files:

src/index.js

```js
function component() {
  const element = document.createElement('div');

  // Lodash, currently included via a script, is required for this line to work
  element.innerHTML = _.join(['Hello', 'webpack'], ' ');

  return element;
}

document.body.appendChild(component());
```

```html
index.html

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>Getting Started</title>
    <script src="https://unpkg.com/lodash@4.17.20"></script>
  </head>
  <body>
    <script src="./src/index.js"></script>
  </body>
</html>
```

What's the problem with this approach?

In this example, there are implicit dependencies between the script tags. The index.js file depends on lodash being included in the page before it runs. This is because index.js never explicitly declared a need for lodash; it assumes that the global variable _ exists.

There are problems with managing JavaScript projects this way:

- It is not immediately apparent that the script depends on an external library.
- If a dependency is missing, or included in the wrong order, the application will not function properly.
- If a dependency is included but not used, the browser will be forced to download unnecessary code.

Let's use webpack to manage these scripts instead.

## Creating a Bundle

Adjust the directory structure by separating the "source" code (./src) from our "distribution" code (./dist). The "source" code is the code that we'll write and edit. The "distribution" code is the minimized and optimized output of our build process that will eventually be loaded in the browser.

Later on in another [guide](https://webpack.js.org/guides/output-management/#setting-up-htmlwebpackplugin), we will generate index.html rather than edit it manually.

To bundle the lodash dependency with index.js, we'll need to install the library locally:

`npm install --save lodash`

Note: When installing a package that will be bundled into the production bundle, use npm install --save. If you're installing a package for development purposes (e.g. a linter, testing libraries, etc.) then you should use npm install --save-dev.

Now, let's import lodash in our index.js script and remove the script tag for lodash in index.html. Furthermore, in the html file, we should remove the script tag for index.js, as it will be replaced with a single script tag for the bundled file.

## Configuration

A configuration file allows far more flexibility than CLI usage. We can specify loader rules, plugins, resolve options and many other enhancements this way.

Using a configuration file is more efficient than having to manually type in a lot of commands in the terminal.

`npx webpack --config webpack.config.js`

Note: If a webpack.config.js is present, the webpack command picks it up by default. We use the --config option here only to show that you can pass a configuration of any name. This will be useful for more complex configurations that need to be split into multiple files.

TODO:

- Note that webpack will not alter any code other than import and export statements. If you are using other ES2015 features, make sure to use a transpiler such as [Babel](https://webpack.js.org/loaders/babel-loader) via webpack's loader system.

## NPM Scripts

Adjust our package.json by adding an npm script:

package.json

```json
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack"
   },
```

Now the `npm run build` command can be used in place of the npx command we used earlier. Note that within scripts we can reference locally installed npm packages by name the same way we did with npx. This convention is the standard in most npm-based projects because it allows all contributors to use the same set of common scripts.

## Asset Management

You can include any type of file for which there is a loader or built-in Asset Modules support. This means that the same benefits listed above for JavaScript (e.g. explicit dependencies) can be applied to everything used in building a website or web app. Let's start with CSS

In order to import a CSS file from within a JavaScript module, you need to install and add the style-loader and css-loader to your module configuration:

`npm install --save-dev style-loader css-loader`

Webpack uses a regular expression to determine which files it should look for and serve to a specific loader. In this case, any file that ends with .css will be served to the style-loader and the css-loader.

This enables you to import './style.css' into the file that depends on that styling. Now, when that module is run, a style tag with the stringified css will be inserted into the head of your html file.

Todo:

- [Minimize](https://webpack.js.org/plugins/mini-css-extract-plugin/#minimizing-for-production) css for better load times in production. On top of that, loaders exist for pretty much any flavor of CSS you can think of – postcss, sass, and less to name a few.

Load images like backgrounds and icons using the built-in Asset Modules.

Now, when you import MyImage from './my-image.png', that image will be processed and added to your output directory and the MyImage variable will contain the final url of that image after processing. When using the css-loader, as shown above, a similar process will occur for url('./my-image.png') within your CSS. The loader will recognize this is a local file, and replace the './my-image.png' path with the final path to the image in your output directory. The html-loader handles `<img src="./my-image.png" />` in the same manner.

[Load fonts and data](https://webpack.js.org/guides/asset-management/#loading-fonts)

## Output Management

[Documentation](https://webpack.js.org/guides/output-management/)

So far we've manually included all our assets in our index.html file, but as your application grows and once you start using hashes in filenames and outputting multiple bundles, it will be difficult to keep managing your index.html file manually.

## Setting up HtmlWebpackPlugin

First install the plugin and adjust the webpack.config.js file:

`npm install --save-dev html-webpack-plugin`

HtmlWebpackPlugin by default will generate its own index.html file, even though we already have one in the dist/ folder. This means that it will replace our index.html file with a newly generated one.

## Cleaning up the /dist folder

In general it's good practice to clean the /dist folder before each build, so that only used files will be generated. Let's take care of that with output.clean option.

Now run an npm run build and inspect the /dist folder. If everything went well you should now only see the files generated from the build and no more old files!

## Development

Warning: Development tools are only meant for development, please avoid using them in production!

Let's start by setting mode to 'development' and title to 'Development'.

## Using source maps

In order to make it easier to track down errors and warnings, JavaScript offers [source maps](https://blog.teamtreehouse.com/introduction-source-maps), which map your compiled code back to your original source code.

## Choosing a Development Tool

It quickly becomes a hassle to manually run npm run build every time you want to compile your code.

There are a couple of different options available in webpack that help you automatically compile your code whenever it changes. In most cases, you probably would want to use webpack-dev-server.

Now run npm run watch from the command line and see how webpack compiles your code. You can see that it doesn't exit the command line because the script is currently watching your files.

The optimization.runtimeChunk: 'single' was added because in this example we have more than one entrypoint on a single HTML page. Without this, we could get into trouble described here. Read the Code Splitting chapter for more details.

Warning
webpack-dev-server doesn't write any output files after compiling. Instead, it keeps bundle files in memory and serves them as if they were real files mounted at the server's root path. If your page expects to find the bundle files on a different path, you can change this with the devMiddleware.publicPath option in the dev server's configuration.