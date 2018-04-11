# Plugin

    git checkout 8158a3dad5bd4d051ec17916ef63e903296694ff

## Event
express/plugin.js
```javascript

// Express - Plugin - Copyright TJ Holowaychuk <tj@vision-media.ca> (MIT Licensed)

// --- Event

var Event = exports.Event = Class({
  init: function(name) {
    this.name = name
    this.request = Express.server.request
    this.response = Express.server.response
  }
})

// --- Helpers

exports.trigger = function(name) {
  $(Express.plugins).each(function(plugin){
    plugin.trigger(new Event(name))
  })
}

/**
 * Push _plugin_ to Express` plugin stack.
 *
 * @param  {Plugin} plugin
 * @api public
 */

exports.use = function(plugin) {
  Express.plugins.push(new plugin)
}

// --- Plugin

exports.Plugin = Class({
  trigger: function(e) {
    if ('on' in this)
      if (e.name in this.on)
        this.on[e.name].call(this, e)
  }
})
```
   git checkout 8248cb01e60e9c859443a8a3ca436ed12bb1968b 
    
## Added common logger 
    
express/plugin/logger.js
```javascript

// Express - Logger - Copyright TJ Holowaychuk <tj@vision-media.ca> (MIT Licensed)

var months = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']

function format(date) {
    var d = date.getDate(),
        m = months[date.getMonth()],
        y = date.getFullYear(),
        h = date.getHours(),
        mi = date.getMinutes(),
        s = date.getSeconds()
    return (d < 10 ? '0' : '') + d + '/' + m + '/' + y + ' ' +
           (h < 10 ? '0' : '') + h + ':' + (mi < 10 ? '0' : '') + 
           mi + ':' + (s < 10 ? '0' : '') + s
}
 
exports.Logger = Plugin.extend({
  on: {
    response: function(event) {
      var response = event.response,
          request = event.request,
          uri = request.uri,
          headers = request.headers
      puts([headers.host, 
            '-', 
            '-',
            '[' + format(new Date) + ']', 
            '"' + request.method.toUpperCase() + ' ' + uri.path + ' HTTP/' + request.httpVersion + '"',
            response.status,
            response.headers['content-length'] || 0].join(' '))
    }
  }
})
```

```javascript
use(require('express/plugins/common-logger').Logger)
```

## Add ContentLength plugin

    git checkout dea4ac986f7cd27cbb7dc2473a7dbef2c77f84e4

lib/express/plugins/content-length.js
```javascript
// Express - ContentLength - Copyright TJ Holowaychuk <tj@vision-media.ca> (MIT Licensed)

exports.ContentLength = Plugin.extend({
  on: {
    response: function(event) {
      var response = event.response
      response.headers['content-length'] =
        response.headers['content-length'] ||
          response.body.length
    }
  }
})
```

    git checkout 87569946f4afcc8fe2a71a5df666d023aa501d2a

usage:
```javascript
use(require('express/plugins/content-length').ContentLength)
```

    git checkout 938dc062d48fa825b453db41d926029fccb27637
    
## Add Method-override 

```javascript
 exports.MethodOverride = Plugin.extend({
   on: {
     request: function(event) {
-      //if (params('__method__'))
-      //  event.request.
+      if (param('__method__'))
+       event.request.method = param('__method__').toLowerCase()
     }
   }
 })
```

usage: 

```javascript
  require.paths.unshift("./lib")
  require('express')
+ require('express/plugins')
 
- use(require('express/plugins/method-override').MethodOverride)
- use(require('express/plugins/content-length').ContentLength)
- use(require('express/plugins/common-logger').Logger)
+ use(MethodOverride)
+ use(ContentLength)
+ use(Logger)
 set('views', dirname(__filename) + '/views')
 
 get('/user/:id?', function() {
-  p(Express.server.request.uri)
   if (param('id'))
     return 'Viewing user ' + param('id')
   return 'Your user account'
```

## Add EJS


```javascript
 * Copyright (c) 2009, Howard Rauscher
 * Licensed under the MIT License
 *
 * base on:
 * Simple JavaScript Templating (http://ejohn.org/blog/javascript-micro-templating/)
 * John Resig - http://ejohn.org/ - MIT Licensed
 */
 
(function(){
    var cache = {};
 
    var ejs = this.ejs = {};
 
    ejs.parse = function tmpl(str, options) {
        options = options || {};
        options.context = options.context || {};
        options.locals = options.locals || {};
 
        // Figure out if we're getting a template, or if we need to
        // load the template - and be sure to cache the result.
        var fn = cache[str] ||
 
        // Generate a reusable function that will serve as a template
        // generator (and which will be cached).
        new Function("obj",
            "var p=[];" +
 
            // Introduce the data as local variables using with(){}
            "with(obj){p.push('" +
 
            // Convert the template into pure JavaScript
            str
                .replace(/\-%>(\n|\r)/g, "%>")
                .replace(/[\t\b\f]/g, " ")
                .replace(/[\n\r]/g, "\f")
                .split("<%").join("\t")
                .replace(/((^|%>)[^\t]*)'/g, "$1\r")
                .replace(/\t=(.*?)%>/g, "',$1,'")
                .split("\t").join("');")
                .split("%>").join("p.push('")
                .split("\r").join("\\'").replace(/\f+/g, '\\n') +
            "');}return p.join('');");
 
        cache[str] = fn;
 
        // Provide some basic currying to the user
        return fn.call(options.context, options.locals);
    };
})();
 
exports.parse = ejs.parse;
```

