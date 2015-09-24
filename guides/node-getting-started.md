#Getting Started with Schema


##Bootstrapping: 

###Create Package file for npm in your projects root directory.

package.js
```
{
  "name": "<Your App>",
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
    "lodash": "^3.10.1",
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

Then run
```
npm install
```

##1 - Node Server Setup:

###Create a server.js file 
server.js
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

router.use(bodyParser.urlencoded({
  extended: true
}));
router.use(express.static(path.resolve(__dirname, '<directory -location>')));
router.use(passport.initialize());
router.use(passport.session());
```



##2 - REST Middleware and Custom Services: server.js -continued

###Login Example:
```
router.post('/login/', function(req, res, next) {

  client.get('/accounts/{email}', {
    email: req.body.username,
    password: req.body.password
  }, function(records) {
    var authenticate = function() {
      if (records) {
        //Send back token or Session
        res.json({
          token: token
        });
      }
      else {
        res.json({
          "message": "No User Found",
          "error": "true"
        });
      }
    };
    authenticate();
  });
  
});
```

###Sign Up Example:
```
router.post('/signup/', function(req, res, next) {

  client.get('/accounts/', {}, function(records) {
    //console.log(records.toObject().results);
    var authenticate = function() {
      var query = _.find(records.toObject().results, {
        'email': req.body.email
      });
      if (query) {
        res.json({
          "message": "User Exsists",
          "error": "true"
        });
      }
      else {
        console.log(req.body);
        client.post('/accounts', {
          email: req.body.email,
          password: req.body.password,
          type: 'individual',
          first_name: req.body.first_name,
          last_name: req.body.last_name
        }, function(result) {
          console.log(result);
          res.json(result);
        });

      }
    };
    authenticate();
  });

});
```



