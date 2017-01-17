# Express in Action
Book Notes

## Review

Examples are well contained and start with the most basic units.  I hate books with long, multi-chapter examples.

Middleware description is fantastic.

Async description was great.

Really awesome that he starts with high level overviews of the main topics before diving into the details.

Some typos and small bugs, but nothing that should hang you up if you're reading closely.

Book is funny with several Celine Dion references and variable names like horribleRegexp.  "or maybe you’re a donut enthu- siast and have gigabytes of donut photos to serve your hungry viewers" made me lol.


## Chapter 1: What is Express?

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



## CHAPTER 3: Foundations of Express

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


## Chapter 4: Middleware

Conceptually, Middleware is the biggest part of Express.  Node forces you to write one giant function for your entire app and Express lets you break up the requests into various functions.

Web servers listen for requests, parse those requests, and send responses.

When you call `res.end` or `response.end`, it signals to Node that the response is done and ready to be sent over the wire.

In Express, the request and response objects are passed through an array of functions, called the middleware stack.

Each function in the middleware stack takes three arguments: `function(request, response, next)`

When `next` is called, Express will go to the next function in the stack.

`request` is an object that represents the incoming HTTP request.

`response` is an object that represents the outgoing HTTP response.

`next` is a function that will go to the next middleware when called.

Eventully, one of the middleware functions must call `res.end` which will end the request.

You need to use `next()` all over the place in Express because Express is asyncronous.  If Express was synchronous, `next()` wouldn't be necessary.

The order of the middleware stack is important.

Here is the code for a simple little app that serves static files.

```javascript
var express = require("express");
var path = require("path");
var fs = require("fs");

var app = express();

app.use(function(req, res, next) {
  console.log("Request IP: " + req.url);
  console.log("Request date: " + new Date());
  next();
});

app.use(function(req, res, next) {
  var filePath = path.join(__dirname, "static", req.url);
  fs.stat(filePath, function(err, fileInfo) {
    if (err) {
      next();
      return;
    }
    if (fileInfo.isFile()) {
      res.sendFile(filePath);
    } else {
      next();
    }
  });
});

app.use(function(req, res) {
  res.status(404);
  res.send("File not found!");
});

app.listen(3000, function() {
  console.log("App started on port 3000");
});
```

The Morgan logger is maintained by the Express core team.  It's not build into Express.

This is how Morgan can be used to log:

```javascript
var morgan = require("morgan");
app.use(morgan("short"));
```

`morgan` is a function and `morgan("short")` returns a middleware function.

This can be written more explicitly as follows:

```javascript
var morgan = require("morgan");
var morganMiddleware = morgan("short");
app.use(morganMiddleware);
```

Here is how the static file server app can be refactored if we use the built-in code.

```javascript
var express = require("express");
var path = require("path");
var morgan = require("morgan");

var app = express();

app.use(morgan("short"));

var staticPath = path.join(__dirname, "static");
app.use(express.static(staticPath));

app.use(function(req, res) {
  res.status(404);
  res.send("File not found!");
});

app.listen(3000, function() {
  console.log("App started on port 3000");
});
```

There are two types of middleware: regular middleware that takes three arguments and error-handling middleware that take four arguments.

When your app is in error mode, Express ignores all the regular middleware and only executes the error-handling middleware.

Calling next with no arguments will exit error mode and move onto the next normal middleware; calling it with an argument will continue onto the next error-handling middleware if one exists.

To reiterate more precisely, “no errors” means “next was never called with any arguments.”  `next(new Error ("Something bad happened!"))` is an example of calling next with an argument.

The convention is to put the error handling middleware at the end of the middleware stack.

Here is an example of how to enter error mode if a file fails to be sent.

```javascript
app.use(function(req, res, next) {
  res.sendFile(filePath, function(err) {
    if (err) {
      next(new Error("Error sending file!"));
    }
  });
});
```

Here is some simple middleware that logs errors, but doesn't respond to the error.

```javascript
app.use(function(err, req, res, next) {
  console.error(err);
  next(err); // continues to next error handling middleware
});
```

Here's how to respond to the error with a 500 status code:

```javascript
app.use(function(err, req, res, next) {
  res.status(500);
  res.send("Internal server error.");
});
```

Express uses the number of arguments of the function to determine which middleware handles errors and which does not.

express.static is the only piece of middleware that's bundled with Express.


## Chapter 5: Routing

Routing lets you match different requests to different request handlers.  It's one of the major features of Express.

We can grab parameters to use with routes.

```javascript
app.get("/users/:userid", function(req, res) {
   var userId = parseInt(req.params.userid, 10);
});
```

You can also use regular expressions to match routes.

The following code will match routes like /users/123 and /users/456, but won't match /uses/olivia.

```javascript
app.get(/^\/users\/(\d+)$/, function(req, res) {
  var userId = parseInt(req.params[0], 10);
  // ...
});
```

Routers can be used to split up the app.  Routers are how you break up the `app.js` file.  From the Express documentation:

A router is an isolated instance of middleware and routes. Routers can be thought of as “mini” applications only capable of performing middleware and routing. Every express application has a built-in app router.

Here's an app.js file that uses a router.

```javascript
var express = require("express");
var path = require("path");
var apiRouter = require("./routes/api_router");

var app = express();

var staticPath = path.resolve(__dirname, "static");
app.use(express.static(staticPath));

app.use("/api", apiRouter);

app.listen(3000);
```

And here's the routes/api\_router.js file.

```javascript
var express = require("express");

var ALLOWED_IPS = [
  "127.0.0.1",
  "123.456.7.89"
];

var api = express.Router();

api.use(function(req, res, next) {
  var userIsAllowed = ALLOWED_IPS.indexOf(req.ip) !== -1;
  if (!userIsAllowed) {
    res.status(401).send("Not authorized!");
  } else {
    next();
  }
});

api.get("/users", function(req, res) { /* ... */ });
api.post("/user", function(req, res) { /* ... */ });

api.get("/messages", function(req, res) { /* ... */ });
api.post("/message", function(req, res) { /* ... */ });

module.exports = api;
```

Middleware can be mounted at a given prefix.

Static files can be served from multiple directories.

```javascript
var publicPath = path.resolve(__dirname, "public");
var userUploadsPath = path.resolve(__dirname, "user_uploads");

app.use(express.static("/public", publicPath));
app.use(express.static("/uploads", userUploadsPath));
```

SSL - Secure Sockets Layer

I don't need to dig into HTTPS/SSL at this time because I can rely on Heroku's built-in features.


## Chapter 6: Building APIs

A better name for API would be "software interface".  A UI interface is meant to be consumed by huamans and a software interface is meant to be consumed by code.

We'll build APIs that receive HTTP requests and respond with JSON data.

Fundamentals of an Express API:

1. Take a request
2. Parse the request
3. Respond with a JSON object and a HTTP status code

Here's an example of a little API that returns a random number.

```javascript
var express = require("express");
var app = express();

app.get("/random/:min/:max", function(req, res) {
  var min = parseInt(req.params.min);
  var max = parseInt(req.params.max);

  if (isNaN(min) || isNaN(max)) {
    res.status(400);
    res.json({ error: "Bad request." });
    return;
  }

  var result = Math.round((Math.random() * (max - min)) + min);

  res.json({ result: result });
});

app.listen(3000, function() {
  console.log("App started on port 3000");
});
```

If you go to localhost:3000/random/10/1000, you can see the random numbers that are generated.

Let's dissect this bit of code:

```javascript
if (isNaN(min) || isNaN(max)) {
  res.status(400);
  res.json({ error: "Bad request." });
  return;
}
```

If the request doesn't contain two numbers then we do three things:

1. Set the HTTP status code to 400
2. Send a JSON object
3. Return (if we didn't return, the rest of the code in the function would have been executed)

HTTP verbs are also known as HTTP methods.  A client sends an HTTP request to the server with a method. The client can choose any method it wants, but there are a handful that are used. The server sees that method and responds accordingly.

Web applications typically use the following four HTTP methods:

1. GET
2. POST
3. PUT - a better name would be update / change
4. DELETE

APIs should be versioned so changes aren't breaking.

You can use a router to create a file that corresponds to version 1 of your API.  See the following `api1.js` file:

```javascript
var express = require("express");

var api = express.Router();

api.get("/timezone", function(req, res) {
  res.send("Sample response for /timezone");
});

api.get("/all_timezones", function(req, res) {
  res.send("Sample response for /all_timezones");
});

module.exports = api;
```

Here is how the v1 and v2 API routers can be used in the `app.js` file:

```javascript
var express = require("express");

var apiVersion1 = require("./api1.js");
var apiVersion2 = require("./api2.js");

var app = express();

app.use("/v1", apiVersion1);
app.use("/v2", apiVersion2);

app.listen(3000, function() {
  console.log("App started on port 3000");
});
```

Every HTTP response comes with a HTTP status code.

In Express, the default status code is 200.

You can set your own status code `res.status(404);`

Express extends the raw HTTP response object that Node gives you.

Some important HTTP status codes:

200: Everything about the request and response went through fine

201: Similar to 200, but it's used when a resource is created

301: Moved permanently

303: When a resource is created and you want to redirect to a new page

400 range: When the client made a mistake and it's not the server's fault.  There was something wrong with the request.

401: Occurs when the user isn't logged in

403: When the user is logged in but the don't have the permissions to make the request

404: When the page is not found

500 range: These are the server's fault.


## Chapter 7: View Templates - Pug and EJS

Express is unopinionated and required a lot of third party accessories to build an application.

Pug and EJS are view engines.

Pug used to be known as Jade, but changed it's name for legal resons.

Here is an EJS snipped that illustrates EJS's main features.

```javascript
Hi <%= name %>!
You were born in <%= birthyear %>, so that means you’re <%= (new Date()).getFullYear() - birthyear %> years old.
<% if (career) { -%>
  <%=: career | capitalize %> is a cool career!
<% } else { -%>
  Haven’t started a career yet? That’s cool.
<% } -%>
Oh, let’s read your bio: <%- bio %> See you later!
```

Pass the following context to EJS:

```javascript
{
  name: "Tony Hawk",
  birthyear: 1968,
  career: "skateboarding",
  bio: "<b>Tony Hawk</b> is the coolest skateboarder around."
}
```

This yields the following result:

```
Hi Tony Hawk!
You were born in 1968, so that means you’re 47 years old.
Skateboarding is a cool career!
Oh, let’s read your bio: Tony Hawk is the coolest skateboarder around. See you later!
```

`<% expression %>` prints the results of a JavaScript expression.

`<%- expression %>` prints the results of an expression and escapes any HTML that might be inside (this is more secure)

`<% expression -%>` avoids adding extraneous newlines.

EJS templates can be included in other EJS templates:

```javascript
<% include header %>
  <h1>Welcome to my page!</h1>
  <p>This is a pretty cool page, I must say.</p>
<% include footer %>
```

You can easily define your own EJS filters.  Here's a filter that sums all the elements in an array.

```javascript
ejs.filters.sum = function(arr) {
  var result = 0;
  for (var i = 0; i < arr.length; i ++) {
    result += arr[i];
  }
  return result;
};
```

You can use it just like any other filter:

```
<%=: myarray | sum %>
```


## Chapter 8: Persisting Your Data with MongoDB

Mongoose is to Mongo as Sequelize is to SQL

The author recommends using the Sequelize package if you're using a SQL database

A Mongo database has many collections.

A Mongo collection has many documents.

Documents don't have to have the same properties.  It's a NoSQL data store.

Every document needs to have a unique \_id property

Documents can embed other documents in Mongo.  A blog post document could include a comments document (for all the comments associated with that blog post).

You need to run Mongo locally on your machine in development.  Just like you need to run Postgres locally on your machine in development.

Mongo stores everything in BSON, which is a binary format.

Here's an example of the user schema in `models/user.js`:

```javascript
var mongoose = require("mongoose");

var userSchema = mongoose.Schema({
  username: { type: String, required: true, unique: true },
  password: { type: String, required: true },
  createdAt: { type: Date, default: Date.now },
  displayName: String,
  bio: String
});
```

Here's an example of a simple method that can be added to the user model.

```javascript
userSchema.methods.name = function() {
  return this.displayName || this.username;
};
```

Let's add some code that will hash the user's password before saving it to the database.  This is a pre-save action.

```javascript
var bcrypt = require("bcrypt-nodejs");
var SALT_FACTOR = 10;

var noop = function() {};

userSchema.pre("save", function(done) {
  var user = this;
  if (!user.isModified("password")) {
    return done();
  }

  bcrypt.genSalt(SALT_FACTOR, function(err, salt) {
    if (err) { return done(err); }
    bcrypt.hash(user.password, salt, noop, function(err, hashedPassword) {
      if (err) { return done(err); }
      user.password = hashedPassword;
      done();
    });
  });
});
```

Skipped the rest of this chapter.



## Chapter 9: Testing Express Applications

TDD cycle: red, green, refactor

Testing is just one way to get more confident in your code.  Peer reviews and code linters are also good methods.

Istanbul is a good tool for looking at the code coverage of a test suite.

Mocha is a JavaScript testing framework that was created by the creator of Express.

Mocha is a testing framework and Chai is an assertion library.  Mocha waits for assertion libraries to throw errors.

Let's write some tests for a function that capitalizes the first letter of a string.

Let's start with this `package.json` file:

```javascript
{
  "private": true,
  "devDependencies": {
    "chai": "^1.9.2",
    "mocha": "^2.0.1"
  },
  "scripts": {
    "test": "mocha"
  }
}
```

This is the `capitalize()` function that will be tested:

```
function capitalize(str) {
  var firstLetter = str.charAt(0).toUpperCase();
  var rest = str.slice(1).toLowerCase();
  return firstLetter + rest;
}

module.exports = capitalize;
```

And here are the tests that use the Mocha testing framework and the Chai assertion library:

```javascript
var capitalize = require("../capitalize");

var chai = require("chai");
var expect = chai.expect;

describe("capitalize", function() {
  it("capitalizes single words", function() {
    expect(capitalize("cats")).to.equal("Cats");
  });

  it("works for the empty string", function() {
    expect(capitalize("")).to.equal("");
  });

  it("works for strings without letters", function() {
    expect(capitalize("234")).to.equal("234");
  });
});
```

Mocha has a built-in `beforeEach()` function.

You can also make sure that code throws an error with certain arguments.

```javascript
it("throws an error if passed a number", function() {
  expect(function() { capitalize(123); }).to.throw(Error);
});
```

You can use the `not()` method to make sure your code does not do something.

```javascript
it("changes the value", function() {
  expect(capitalize("foo")).not.to.equal("foo");
});
```

Supertest spools up your Express server and sends requests to it.

Most browsers send a header called User-Agent that identifies the type of browser to the server.

Let's use Supertest to test a plain text API.

Here is the `app.js` file for a simple little app that sends a plain text response with the user agent.

```javascript
var express = require("express");

var app = express();

app.set("port", process.env.PORT || 3000);

app.get("/", function(req, res) {
  res.type("text");
  res.send(req.headers["user-agent"]);
});

app.listen(app.get("port"), function() {
  console.log("App started on port " + app.get("port"));
});

module.exports = app;
```

Here are the tests for the plain text API above.

```javascript
var app = require("../app");
var supertest = require("supertest");

describe("plain text response", function() {

  it("returns a plain text response", function(done) {
    supertest(app)
      .get("/")
      .set("User-Agent", "my cool browser")
      .set("Accept", "text/plain")
      .expect("Content-Type", /text\/plain/)
      .expect(200)
      .end(done);
  });

  it("returns your User Agent", function(done) {
    supertest(app)
      .get("/")
      .set("User-Agent", "my cool browser")
      .set("Accept", "text/plain")
      .expect(function(res) {
        if (res.text !== "my cool browser") {
          throw new Error("Response does not contain User Agent");
        }
      })
      .end(done);
  });

});
```

The Cheerio module is basically jQuery for Node.  It lets you parse the DOM for your tests.

I skipped the Cheerio stuff because I don't really like view tests.




