# How To Add Bootstrap to a Ruby on Rails Application

When developing a application, you may be interested in adding styles to your project to facilitate user engagement. One way to do this is by adding [Bootstrap](https://getbootstrap.com/), an HTML, CSS, and JavaScript framework designed to simplify the process of making web projects responsive and mobile ready. By implementing Bootstrap in a Rails project, you can integrate its layout conventions and components into your application to make user interactions with your site more engaging.

In this section, you will add Bootstrap to `bnb-library` that uses the [webpack](https://webpack.js.org/) bundler to serve its JavaScript and CSS assets. The goal will be to create a visually appealing site that users can interact with to share information about books:

## Step 1 — Installing Bootstrap and Adding Custom Styles

In this step, you will add Bootstrap to your project, along with the tool libraries that it requires to function properly. This will involve importing libraries and plugins into the application's webpack entry point and environment files. It will also involve creating a custom style sheet in your application's `app/javascript` directory, the directory where the project's JavaScript assets live.

First, use `yarn` to install Bootstrap and its required dependencies:
```
dip yarn add bootstrap jquery popper.js
```
Many of Bootstrap's components require [JQuery](https://jquery.com/) and [Popper.js](https://popper.js.org/), along with Bootstrap's own custom plugins, so this command will ensure that you have the libraries you need.

Next, open your main webpack configuration file, `config/webpack/environment.js` with VSCode:
```rb
# config/webpack/environment.js
```
Inside the file, add the webpack library, along with a [ProvidePlugin](https://webpack.js.org/plugins/provide-plugin/) that tells Bootstrap how to interpret JQuery and Popper variables.

Add the following code to the file:
```js
# config/webpack/environment.js
-------------------------------

const { environment } = require('@rails/webpacker')
const webpack = require("webpack")

environment.plugins.append("Provide", new webpack.ProvidePlugin({
  $: 'jquery',
  jQuery: 'jquery',
  Popper: ['popper.js', 'default']
}))

module.exports = environment
```
The `ProvidePlugin` helps us avoid the multiple `import` or `require` statements we would normally use when working with JQuery or Popper modules. With this plugin in place, webpack will automatically load the correct modules and point the named variables to each module's loaded exports.

Save and close the file when you are finished editing.

Next, open your main webpack entry point file, `app/javascript/packs/application.js`:
```rb
# app/javascript/packs/application.js
```
Inside the file, add the following `import` statements to import Bootstrap and the custom `scss` styles file that you will create next:
```js
# app/javascript/packs/application.js
-------------------------------------

import Rails from "@rails/ujs"
import Turbolinks from "turbolinks"
import * as ActiveStorage from "@rails/activestorage"
import "channels"

Rails.start()
Turbolinks.start()
ActiveStorage.start()

import "controllers"
import "bootstrap"
import "../stylesheets/application"
```
Save and close the file when you are finished editing.

Next, create a `stylesheets` directory in VSCode for your application style sheet:
```rb
# app/javascript/stylesheets
```

  Open the custom styles file:
This is an scss file, which uses Sass instead of CSS. Sass, or Syntactically Awesome Style Sheets, is a CSS extension language that lets developers integrate programming logic and conventions like shared variables into styling rules.
In the file, add the following statements to import the custom Bootstrap scs s styles and Google fonts for the project:
  nano app/javascript/stylesheets/application.scss
   ~/rails-
bootstrap/app/javascript/stylesheets/application
.scss
  @import "~bootstrap/scss/bootstrap";
@import url('https://fonts.googleapis.com/css?family=Merriweat
her:400,700');
Next, add the following custom variable definitions and styles for the application:
 
~/rails-
bootstrap/app/javascript/stylesheets/application
.scss
   .. .
$white: white; $black: black;
.navbar {
        margin-bottom: 0;
        background: $black;
}
body {
} h1, h2 {
} p{
background: $black;
color: $white;
font-family: 'Merriweather', sans-serif;
font-weight: bold;
font-size: 16px;
color: $white;
}
a:visited {
        color: $black;
 
 }
.jumbotron {
background: #0048CD; color: $white; text-align: center; p{
                color: $white;
                font-size: 26px;
} }
.link {
        color: $white;
}
.btn-primary {
        color: $white;
        border-color: $white;
        margin-bottom: 5px;
}
.btn-sm {
        background-color: $white;
        display: inline-block;
}
img,
video,
audio {
margin-top: 20px;
max-width: 80%;
 
Save and close the file when you are finished editing.
You have added Bootstrap to your project, along with some custom styles. Now you can move on to integrating Bootstrap layout conventions and components into your application files.
