# Express in Action
Book Notes

The Express middleware stack is effectively an array of functions.

## Part 1: Intro
Express makes it easier to write web applications.

Chapter 1: What is Express?
Node.js encourages an asynchronous coding style, making for faster code while avoiding multithreaded nightmares.

Express exists to limit the boilerplate code that's needed to work with Node.js and provide better APIs.

Express is easily extensible with third party libraries.

Node is performant because it's async.  If it has to talk to the database, it can send the request and do other stuff in the meantime.  Rails can only process one request at a time.

Node make it so developers don't have to switch context from the backend to the frontend.  He thinks this is the main advantage of Node.

When you create a web server with Node, you write a single JavaScript function for your entire application.

Express isn't opinionated and doesn't give you a rigid structure.  Express is a minimalist framework.

Middleware in an array of functions.  For example: log the request => authorize the user => send static page

Middleware is fairly standardized and there are a lot of third party libraries in middleware format.

Express is unopinionated about how you scale your app.

Express allows you to create miniapplications that are called routers.  This feature is very useful for bigger applications.

Express isn't like Rails.  Rails is a more opinionated framework with lots of features.

Mongo is a JavaScript friendly database... not sure why.

We look at two templating engines in this book:
- EJS: looks a lot like HTML
- Pug: tries to fix HTML with a radical new syntax

Express is just a Node.js module, like any other.

It's hard to write web applications with pure Node.  Express was built to make it easier to write web applications.


## CHAPTER 2: The Basics of Node.js

Node has a standard module system called CommonJS

You can require built-in modules, require third party modules, or make your own modules with Node.

Here's an example that uses the built-in `url` module:

```javascript
var url = require("url");
var parsedURL = url.parse("http://www.example.com/profile?name=barry");
console.log(parsedURL.protocol);  // "http:"
console.log(parsedURL.host);      // "www.example.com"
console.log(parsedURL.query);     // "name=barry"
```

Every Node project has a package.json file that defines the project's metadata and dependencies.

Every Node project comes with npm.  npm is an official helper for Node that helps you with your Node projects.

Here is an example package.json file:

```javascript
{
  "name": "my-fun-project",
  "author": "Evan Hahn",
  "private": true,
  "version": "0.2.0",
  "dependencies": {}
}
```

Here is the command to install Mustache as a dependency and add the Mustache code to the `node_modules` directory.

```
npm install mustache --save
```

You want to list all of your project dependencies in the `package.json` file so other developers can download all the required dependencies and run your code.

`npm init` helps you create the `package.json` file.

Here is an example of a module that we can create.

```javascript
function randomInteger() {
  return Math.floor((Math.random() * 100));
}
module.exports = randomInteger;
```

You can only export one variable.  In this case, we're exporting a function, but we could also export strings, objects, numbers, or arrays.

Here is how the function can be used in another file.

```javascript
var randomInt = require("./random-integer");
console.log(randomInt());  // 12
console.log(randomInt());  // 77
console.log(randomInt());  // 8
```

Node can't do two things at once, even if two things are happening at the same time.  It's like a human.  You can put muffins in the oven and then go for a jog.  The muffins are in the oven while you are jogging at the same time.  But your not watching the muffins in the oven and jogging at the same time.

Node doesn't do two things at once.  It just doesn't wait while an external resource is working on something.  That's why callbacks are all over the place in Node.

The Node filesystem module (fs) is mainly used for reading and writing files.

Most callbacks in Node are called with an error as their first argument.

Here's a typical Node callback:

```javascript
var fs = require("fs");

var options = { encoding: "utf-8" };
fs.readFile("myfile.txt", options, function(err, data) {
  if (err) {
    console.error("Error reading file!");
    return;
  }
  console.log(data.match(/x/gi).length + " letter X's");
});
```

You should handle all errors in Node.

Asynch web applications can parse many more web requests at once.

There is a great YouTube video: "What the heck is the event loop anyway?"

Node's http module is the most important for Express

Nearly every bit of code in this book is a request handler function or a way to call one.

Here is some code to set up a hello world HTTP server.

```javascript
var http = require("http");

function requestHandler(request, response) {
  console.log("In comes a request to: " + request.url);
  response.end("Hello, world!");
}

var server = http.createServer(requestHandler);

server.listen(3000);
```

This will show "Hello, world!" in the browser and print something in the console every time you visit a different page.

The author recommends installing Node with a version manager, so you can easily change versions and upgrade as necessary.



