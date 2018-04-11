# Redirect 

    git checkout ff293e51045a2567bd64c0e9727c7f24e86816c1
    
lib/express/plugins/redirect

```javascript
// Express - Redirect - Copyright TJ Holowaychuk <tj@vision-media.ca> (MIT Licensed)

/**
 * Redirect to _uri_.
 *
 * When using redirect(home) the resolution
 * is as follows:
 *
 *  - set('home')
 *  - set('basepath')
 *  - '/'
 *
 * When using redirect(back) the HTTP referrer
 * header is used. Commonly misspelled as 'referer'
 * which is supported as well.
 *
 * @param  {string} uri
 * @settings 'home', 'basepath'
 * @api public
 */


exports.redirect = function(uri) {
  header('location', uri)
  halt(302)
}

exports.Redirect = Plugin.extend({
  init: function() {
    this.__super__.apply(arguments)
    process.mixin(GLOBAL, exports)
  },
  on: {
    request: function(event) {
      home = set('home') ||
             set('basepath') ||
             '/'
      back = header('referrer') ||
             header('referer')
    }
  }
})
```

example app

    git checkout 0594725c298c250b82ed3ebf14c35f6277ecab9f

```javascript
require.paths.unshift('lib')
require('express')
require('express/plugins')

configure(function(){
  use(Profiler)
  use(MethodOverride)
  use(ContentLength)
  use(Redirect)
  set('root', dirname(__filename))
  enable('cache views')
})

get('/hello', function(){
  contentType('html')
  return '<h1>World<h1>'
})

get('/user/:id?', function(id) {
  render('user.haml.html', {
    locals: {
      name: id ? 'User ' + id : 'You' 
    }
  })
})

run()
```