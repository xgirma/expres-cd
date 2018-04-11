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
/**
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

## Add hooks

    git checkout a8409ba53dc3cc9f0be798d6a70e3a66cef0730f
    
```javascript
// Express - Hooks - Copyright TJ Holowaychuk <tj@vision-media.ca> (MIT Licensed)

var before = [],
    after = []

exports.before = function(fn) {
  before.push(fn)
}

exports.after = function(fn) {
  after.push(fn)
}

exports.Hooks = Plugin.extend({
  init: function() {
    this.__super__.apply(arguments)
    process.mixin(GLOBAL, exports)
  },
  on: {
    request: function(event) {
      $(before).each(function(fn){
        fn(event)
      })
    },
    
    response: function(event) {
      $(after).each(function(fn){
        fn(event)
      })
    }
  }
})
```

## Add haml and jslint

    git checkout 39719af1caad46c88814de95e791e78f65dc3a19

```javascript
/* ex:ts=2:et: */
/*jslint bitwise: true, browser: true, eqeqeq: true, evil: true, immed: true, newcap: true,
    nomen: true, plusplus: true, regexp: true, undef: true, white: true, indent: 2 */
/*globals */
 
function Haml() {}
 
Haml.to_html = function (json) {
 
  if (typeof json === 'string') {
 
    if (json.substr(0, 3) === '!!!') {
      switch (json.substr(4).toLowerCase()) {
        case 'xml':
          return "<?xml version='1.0' encoding='utf-8' ?>\n";
        case '':
          return '<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">\n';
        case '1.1':
          return '<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd">\n';
        case 'strict':
          return '<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">\n';
      }
    }
    return json;
  }
  if (typeof json === 'object' && json.length !== undefined) {
    if (typeof json[0] === 'object')
    {
      if (json[0].tag !== undefined) {
        return (function () {
          var tag, element, html, attributes;
          element = json.shift();
          tag = element.tag;
          html = tag;
          for (var key in element) {
            if (element.hasOwnProperty(key) && key !== 'tag') {
              html += " " + key + "=\"" + element[key].
                replace("&", "&amp;").
                replace("<", "&lt;").
                replace(">", "&gt;").
                replace("\"", "&quot;") + "\"";
            }
          }
          if (json.length === 0 && (tag === "link" || tag === 'br' || tag === 'input')) {
            return "\n<" + html + " />\n";
          }
          return "\n<" + html + ">" + Haml.to_html(json) + "</" + tag + ">\n";
        }());
      }
      if (json[0].plugin !== undefined) {
          switch (json.shift().plugin) {
            case "javascript":
              return '\n<script type="text/javascript">\n//<![CDATA[\n  ' + json[0].split("\n").join("\n  ") + "\n//]]>\n</script>\n";
            case "css":
              return '\n<style type="text/css">\n  ' + json[0].split("\n").join("\n  ") + "\n</style>\n";
          }
      }
    }
    return json.map(Haml.to_html).join('');
  }
  return JSON.stringify(json);
}
 
Haml.parse = function (text) {
 
  var empty_regex = new RegExp("^[ \t]*$"),
      indent_regex = new RegExp("^ *"),
      element_regex = new RegExp("^(?::[a-z]+|(?:[%][a-z][a-z0-9]*)?(?:[#.][a-z0-9_-]+)*)", "i"),
      scope = this,
      haml, element, stack, indent, buffer, old_indent, mode, last_insert;
 
  // Sortof an instance_eval for Javascript
  function instance_eval(input) {
    var block;
    block = function () { with(scope) { return eval("(" + input + ")"); } };
    return block.call(scope);
  }
 
  function process_embedded_code(string) {
    if (typeof string !== 'string') {
      return string;
    }
    var matches = string.match(/#{([^}]*)}/g);
    if (matches) {
      matches.forEach(function (match) {
        var pair = match.match(/#{([^}]*)}/);
        string = string.replace(pair[0], instance_eval(pair[1]));
      })
    }
    return string;
  }
 
  function flush_buffer() {
    if (buffer.length > 0) {
      mode = "NORMAL";
      element.push(process_embedded_code(buffer.join("\n")));
      buffer = [];
    }
  }
 
  function parse_push() {
    stack.push({element: element, mode: mode});
    var new_element = [];
    mode = "NORMAL";
    element.push(new_element);
    element = new_element;
  }
 
  function parse_pop() {
    var last = stack.pop();
 
    if (element.length === 1) {
      if (typeof element[0] === "string") {
        if (element[0].match(element_regex)[0].length === 0) {
          // Collapse arrays with single string literal
          last.element[last.element.length - 1] = element[0];
        }
      }
    }
    element = last.element;
    mode = last.mode;
  }
 
  function get_indent(line) {
    if (line === undefined) {
      return 0;
    }
    var i = line.match(indent_regex);
    return i[0].length / 2;
  }
 
  function parse_attribs(line) {
    // Parse the attribute block using a state machine
    if (!(line.length > 0 && line.charAt(0) === '{')) {
      return line;
    }
    var l = line.length;
    var count = 1;
    var quote = false;
    var skip = false;
    for (var i = 1; count > 0; i += 1) {
 
      // If we reach the end of the line, then there is a problem
      if (i > l) {
        throw "Malformed attribute block";
      }
 
      var c = line.charAt(i);
      if (skip) {
        skip = false;
      } else {
        if (quote) {
          if (c === '\\') {
            skip = true;
          }
          if (c === quote) {
            quote = false;
          }
        } else {
          if (c === '"' || c === "'") {
            quote = c;
          }
          if (c === '{') {
            count += 1;
          }
          if (c === '}') {
            count -= 1;
          }
        }
      }
    }
    var block = line.substr(0, i);
    (function () {
      element.push(instance_eval(block))
    }.call(this));
    return line.substr(i);
  }
 
  function parse_content(line) {
    // Strip off leading whitespace
    line = line.replace(indent_regex, '');
 
    // Ignore blank lines
    if (line.length === 0) {
      return;
    }
 
    if (mode === 'ELEMENT') {
      parse_pop();
    }
 
    switch (line.charAt(0)) {
    case '/':
      break;
    case '=':
      (function () {
        buffer.push(instance_eval(line.substr(1)));
      }.call(this));
      break;
    case "\\":
      buffer.push(line.substr(1));
      break;
    default:
      buffer.push(line);
      break;
    }
  }
 
  function parse_element(line, selector) {
 
    flush_buffer();
    if (element.length > 0) {
      if (mode === 'ELEMENT') {
        parse_pop();
      }
      parse_push();
    }
    mode = 'ELEMENT';
 
    var classes = selector.match(/\.[^\.#]+/g),
    ids = selector.match(/#[^\.#]+/g),
    tag = selector.match(/^%([^\.#]+)/g),
    plugin = selector.match(/^:([^\.#]+)/g);
    tag = tag ? tag[0].substr(1) : (plugin ? null : 'div');
    plugin = plugin ? plugin[0].substr(1) : null;
 
    line = parse_attribs.call(this, line.substr(selector.length));
 
    var attrs;
    if (typeof element[element.length - 1] === "object") {
      attrs = element[element.length - 1];
    } else {
      attrs = {};
      element.push(attrs);
    }
    if (tag) {
      attrs.tag = tag;
    }
    if (plugin) {
      attrs.plugin = plugin;
    }
    if (ids) {
      for (var i = 0, l = ids.length; i < l; i += 1) {
        ids[i] = ids[i].substr(1);
      }
      if (attrs.id) {
        ids.push(attrs.id);
      }
      attrs.id = ids.join(" ");
    }
    if (classes) {
      for (var i = 0, l = classes.length; i < l; i += 1) {
        classes[i] = classes[i].substr(1);
      }
      if (attrs['class']) {
        classes.push(attrs['class']);
      }
      attrs['class'] = classes.join(" ");
    }
 
    if (selector.charAt(0) === ':') {
      mode = 'RAW';
    } else {
      if (!line.match(empty_regex)) {
        parse_push();
        parse_content.call(this, line, true);
        flush_buffer();
        parse_pop();
      }
    }
  }
 
  function process_plugins() {
    var contents, i;
    switch (element[0].plugin) {
    case 'if':
      var condition = element[0].condition;
      contents = element[1];
      for (i in element) {
        if (element.hasOwnProperty(i)) {
          delete element[i];
        }
      }
      if (condition) {
        var new_element = Haml.parse.call(this, contents);
        for (i in new_element) {
          if (new_element.hasOwnProperty(i)) {
            element[i] = new_element[i];
          }
        }
        element.length = new_element.length;
      }
      break;
    case 'foreach':
      var array, key, value, key_name, value_name;
      array = element[0].array;
      key_name = element[0].key;
      value_name = element[0].value;
      contents = element[1];
      for (i in element) {
        if (element.hasOwnProperty(i)) {
          delete element[i];
        }
      }
      element.length = 0;
      for (key in array) {
        if (array.hasOwnProperty(key)) {
          value = array[key];
          this[key_name] = key;
          this[value_name] = value;
          element.push(Haml.parse.call(this, contents));
        }
      }
      break;
    }
  }
 
  haml = [];
  element = haml;
  stack = [];
  buffer = [];
  mode = 'NORMAL';
  parse_push(); // Prime the pump so we can have multiple root elements
  indent = 0;
  old_indent = indent;
 
  var lines = text.split("\n"),
      line, line_index, line_count;
 
  line_count = lines.length;
  for (line_index = 0; line_index <= line_count; line_index += 1) {
    line = lines[line_index];
 
    switch (mode) {
    case 'ELEMENT':
    case 'NORMAL':
 
      // Do indentation
      indent = get_indent(line);
      if (indent === old_indent + 1) {
        parse_push();
        old_indent = indent;
      }
      while (indent < old_indent) {
        flush_buffer();
        parse_pop();
        old_indent -= 1;
      }
 
      if (line === undefined) {
        continue;
      }
 
      line = line.substr(indent * 2);
 
      // Pass doctype commands through as is
      if (line.substr(0, 3) === '!!!') {
        element.push(line);
        continue;
      }
 
      // Check for special element characters
      var match = line.match(element_regex);
      if (match && match[0].length > 0) {
        parse_element.call(this, line, match[0]);
      } else {
        parse_content.call(this, line);
      }
      break;
    case 'RAW':
      if (get_indent(line) <= indent) {
        flush_buffer();
        process_plugins.call(this);
        mode = "ELEMENT";
        line_index -= 1;
        continue;
      }
      line = line.substr((indent + 1) * 2);
      buffer.push(line);
      break;
    }
  }
 
  if (haml.length === 1 && typeof haml[0] !== 'string') {
    haml = haml[0];
  }
  return haml;
};
 
exports.render = function(content, options) {
  options = options || {}
  var json = Haml.parse.call(options.context || GLOBAL, content)
  p(json)
}
```

template engines supported (ejs, haml, mustache)
