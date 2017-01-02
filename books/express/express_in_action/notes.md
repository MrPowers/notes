# Express in Action
Book Notes

## Review

Examples are well contained and start with the most basic units.

Middleware description is fantastic.

Async description was great.

Really awesome that he starts with high level overviews of the main topics before diving into the details.


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



CHAPTER 3: Foundations of Express

Express is an abstraction layer that's built on top of Node's built-in HTTP server.

At a high level, Express provides four major features:

- middleware: an array of functions
- routing
- extensions to request and response objects
- views

Here is how to add Express to an app: `npm install express --save`

Let's rewrite the hello world example using Express's middleware:

```javascript
var express = require("express");
var http = require("http");

var app = express();

app.use(function(request, response) {
  console.log("In comes a request to: " + request.url);
  response.end("Hello, world!");
});

http.createServer(app).listen(3000);
```

In Node's HTTP server, every request goes through one big function.

One of the functions in the array of middleware should respond to the request.  Otherwise, the browser will hang.

Here is how logging middleware can be added to the hello world app:

```javascript
var express = require("express");
var http = require("http");
var app = express();

// logging middleware
app.use(function(request, response, next) {
  console.log("In comes a " + request.method + " to " + request.url);
  next();
});

// sends the actual response
app.use(function(request, response) {
  response.writeHead(200, { "Content-Type": "text/plain" });
  response.end("Hello, world!");
});

http.createServer(app).listen(3000);
```

Express adds some things to the Node objects, but it doesn't take anything away.  You can still use the Node objects like you did before.

Middleware can change the request or response objects

It's pretty common to use middleware in third party libraries

express.static ships with Express and helps you serve static files.

It's better to use resolve `path.resolve(__dirname, "public")` than hardcode the path `/public` because the hardcoded path isn't cross platform.

There is another framework like Express that's called Connect.  Connect only does middleware.  If you can't find any Express middleware that meets your needs, try the Connect middleware.

Routing is best explained with an example.

```javascript
var express = require("express");
var path = require("path");
var http = require("http");
var app = express();

var publicPath = path.resolve(__dirname, "public");

app.use(express.static(publicPath));

app.get("/", function(request, response) {
  response.end("Welcome to my homepage!");
});

app.get("/about", function(request, response) {
  response.end("Welcome to the about page!");
});

app.get("/weather", function(request, response) {
  response.end("The current weather is NICE.");
});

app.use(function(request, response) {
  response.statusCode = 404;
  response.end("404!");
});

http.createServer(app).listen(3000);
```

The Express methods can also access url params.

```javascript
app.get("/hello/:who", function(request, response) {
  response.end("Hello, " + request.params.who + ".");
  // Fun fact: this has some security issues, which we’ll get to!
});
```

Express extends the request and response objects with methods like `redirect` and `sendFile`.

Here is how an evil IP address can be blacklisted.

```javascript
var express = require("express");
  var app = express();
  var EVIL_IP = "123.45.67.89";
  app.use(function(request, response, next) {
   if (request.ip === EVIL_IP) {
      response.status(401).send("Not allowed!");
    } else {
  next(); }
});
```

Here is how to update the `app.js` file to render some views.

```javascript
app.set("views", path.resolve(__dirname, "views"));
app.set("view engine", "ejs");

app.get("/", function(request, response) {
  response.render("index", {
    message: "Hey everyone! This is my webpage."
  });
});
```

You need to create a `/views/index.ejs` file to be rendered.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Hello, world!</title>
  </head>
  <body>
    <%= message %>
  </body>
</html>
```



