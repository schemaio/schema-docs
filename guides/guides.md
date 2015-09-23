## Getting Started with Schema

0 - Bootstrapping

##Package file: package.js
```
{
  "name": "Your App",
  "version": "0.0.0",
  "description": "",
  "main": "server.js",
  "repository": "",
  "author": "Name <yourname@yourcompany.com>",
  "dependencies": {
    "async": "~0.2.8",
    "express": "~3.2.4",
    "schema-client": "^1.2.5",
    "socket.io": "~0.9.14",
    "uglify": "^0.1.5"
  },
  "devDependencies": {
    "grunt": "^0.4.5",
    "grunt-contrib-jshint": "~0.6.0",
    "grunt-contrib-cssmin": "~0.6.0",
    "grunt-contrib-watch": "^0.6.1",
    "grunt-express": "^1.4.1",
    "grunt-express-server": "^0.5.1"
  }
}


```

##1 - Node Server Setup
```
var http = require('http');
var path = require('path');
var async = require('async');
var socketio = require('socket.io');
var express = require('express');

var bodyParser = require('body-parser');
var Schema = require('schema-client');
//Setup Kountid Key
var client = new Schema.Client('<client-name>', '<client-key>');

// ## SimpleServer `SimpleServer(obj)`
//
// Creates a new instance of SimpleServer with the following options:
//  * `port` - The HTTP port to listen on. If `process.env.PORT` is set, _it overrides this value_.
//
var router = express();
var server = http.createServer(router);
var io = socketio.listen(server);



```



##2 - REST Middleware and Custom Services
```


```


