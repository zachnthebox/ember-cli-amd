# Ember-cli-amd

This addon will dynamically modify `loader.js` to allow it to work in parallel with a separate AMD loader.
*Thanks to [Jack Rowlingson](https://github.com/jrowlingson) for figuring out how to use an AMD loader and ember-cli loader concurrently.*

[View it live](http://esri.github.io/ember-cli-amd/) using the [ArcGIS API for JavaScript](https://developers.arcgis.com/javascript/).

## Features
* Load AMD modules in parallel with [ember-cli/loader.js](https://github.com/ember-cli/loader.js).
* Works with AMD CDN libraries, such as [Dojo](https://dojotoolkit.org/download/) or the [ArcGIS API for JavaScript](https://developers.arcgis.com/javascript/).
* Uses the [RequireJS Optimizer](http://requirejs.org/docs/optimization.html) for fast builds while coding.

## Installation

* `git clone` this repository
* `npm install`
* `bower install`

## Usage

Install to your ember-cli application

* `npm install --save-dev ember-cli-amd`

Provide a list of packages that will be loaded via an AMD loader such as RequireJS or Dojo. You can also provide the source for the loader.
```javascript
// use this in ember-cli-build.js
// Sample if using the ArcGIS API for JavaScript
var app = new EmberApp({
  amd : {
    // Specify the type of AMD loader. Either a CDN path or a local loader ('dojo' or 'requirejs')
    loader: 'https://js.arcgis.com/3.15/',
    // user defined AMD packages to search for in application
    packages: [
      'esri','dojo','dojox','dijit',
      'put-selector','xstyle','dgrid'
    ],
    // Optional: the AMD configuration. It can be either an object or a file path. The path is relative to the root
    // of the project as amdConfig: 'config/amd-config.js'. For the object properties, refer to either the dojo or the requirejs 
    // configuration documentation
    config: {
    },
    // If using a local loader ('dojo' or 'rquirejs'), the path to the AMD library must be provided.
    libraryPath: 'bower_components/amdlibrary',
    // When uing a local loader, we will build the AMD module using requirejs into a single file
    // The following properties allow to control the build
    // Optional: it defaults to vendor/build.js
    outputPath: 'vendor/build.js'    
    // Optional: Will use RequireJS i18n to set the localization, default is 'en-us' 
    locale: 'en-us', 
    // Optional: Will create a dependencies.txt that will list all the AMD dependencies in the application, default is false
    outputDependencyList: true,
    // RequireJS build configuration options
    // Please refere to RequireJS docs for more information
    // http://requirejs.org/docs/optimization.html
    buildConfig: {
      include: [
        'foo/bar/baz'
      ],
      exclude: [
        'lorem/ipsum'
      ],
      paths: {
        'plugins/plugin': 'empty:'
      }
    }
  }
});
```

Update the `index.html` file to allow this addon to add script files as needed.
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>AMDApp</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">

    {{content-for 'head'}}
    <link rel="stylesheet" href="assets/vendor.css">
    <link rel="stylesheet" href="assets/app.css">
    {{content-for 'head-footer'}}
    
  </head>
  <body>
    {{content-for 'amd'}}
    {{content-for 'body'}}
    <!-- We removed the app and vendor js files as they will be inserted by the addon -->
    {{content-for 'body-footer'}}
  </body>
</html>
```

Update the `test/index.html` file to allow this addon to add script files as needed.
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>AMDApp Tests</title>
    <meta name="description" content="">
    <meta name="viewport" content="width=device-width, initial-scale=1">

    {{content-for 'head'}}
    {{content-for 'test-head'}}

    <link rel="stylesheet" href="assets/vendor.css">
    <link rel="stylesheet" href="assets/ember-test.css">
    <link rel="stylesheet" href="assets/test-support.css">

    {{content-for 'head-footer'}}
    {{content-for 'test-head-footer'}}
  </head>
  <body>
    {{content-for 'amd-test'}}
    {{content-for 'body'}}
    {{content-for 'test-body'}}
    <!-- We removed the app, vendor, and test js files as they will be inserted by the addon -->
    {{content-for 'body-footer'}}
    {{content-for 'test-body-footer'}}
  </body>
</html>
```

If using ember-cli-content-security-policy, update this `ENV` object in `config/environment.js` to allow pulling in CDN resources such as with the ArcGIS API for JavaScript.
```javascript
var ENV = {
  ...
  contentSecurityPolicy: {
    'default-src': "'none'",
    'script-src': "'self' 'unsafe-eval' 'unsafe-inline' http://js.arcgis.com/ https://js.arcgis.com/",
    'font-src': "'self'",
    'connect-src': "'self' http://services.arcgis.com/ http://services.arcgisonline.com/",
    'img-src': "'self' http://js.arcgis.com/",
    'style-src': "'self' 'unsafe-inline'",
    'media-src': "'self'"
  }
```

## Example using the CDN resources

```javascript
// ember-cli-build.js
module.exports = function(defaults) {
  
  var app = new EmberApp(defaults, {
    amd :{
      loader: 'https://js.arcgis.com/3.14/',
      amdPackages: [
        'esri','dojo','dojox','dijit',
        'put-selector','xstyle','dbind','dgrid'
      ],
      config: {
        async: true,
        locale: 'en-us',
        isDebug: true
      }
    }
  });
  
  return app.toTree();
};
```

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>ArcGIS App</title>
    <meta name="description" content="">
    <meta name="viewport" content="width=device-width, initial-scale=1">

    {{content-for 'head'}}
    <link rel="stylesheet" href="assets/vendor.css">
    <link rel="stylesheet" href="assets/arcgis-app.css">
    {{content-for 'head-footer'}}
    
  </head>
  <body>
    {{content-for 'amd'}}
    {{content-for 'body'}}
    {{content-for 'body-footer'}}
  </body>
</html>
```

# Running

* `ember server`
* Visit your app at http://localhost:4200.

## Running Tests

* `ember test`
* `ember test --server`

## Building

* `ember build`

## Requirements
* [ember-cli](http://www.ember-cli.com/) 1.13.0 or greater.

## Resources
* For more information on using ember-cli, visit [http://www.ember-cli.com/](http://www.ember-cli.com/).
* To learn more about the ArcGIS API for JavaScript, visit [the developers pages](https://developers.arcgis.com/javascript/).

## Issues

Find a bug or want to request a new feature?  Please let us know by submitting an issue.

## Contributing

Esri welcomes contributions from anyone and everyone. Please see our [guidelines for contributing](https://github.com/esri/contributing).

## Licensing
Copyright 2015 Esri

Licensed under The MIT License(MIT);
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://opensource.org/licenses/MIT

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

A copy of the license is available in the repository's [LICENSE.md](LICENSE.md) file.

