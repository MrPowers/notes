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

