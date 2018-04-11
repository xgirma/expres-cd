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
