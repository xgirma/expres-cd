# Copyright

    git checkout 50b3c2c048a46a7f334106ee98bd7f7b148b370e
    
lib/express.core.js
```javascript
+ // Express - Core - Copyright TJ Holowaychuk <tj@vision-media.ca> (MIT Licensed)

(function(){
  Express = { 
    version : '0.0.1',
    routes  : [],
    
    // --- Break
    
    Break : function(){
      this.toString = function() {
        return 'Express.Break'
      }
    },
    
    // --- Response
    
    response : {
      headers : {},
      cookie : {}
    },
    
    // --- Defaults
    
    defaults : {
      cookie : {
        maxAge : 3600
      },
      route : {
        callback : function() {
          Express.respond('Page or file cannot be found', 'Not Found')
        }
      }
    },
    ...
```