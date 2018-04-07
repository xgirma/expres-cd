# Reorganizing lib directory

    git checkout dca7e9bbd17e2d96a081754469d73f49d35a61c8
    
````diff
- require('express.core')
- require('express.cookie')
- require('express.mime')
- require('express.session')
- require('express.view')
+ require('express/core')
+ require('express/cookie')
+ require('express/mime')
+ require('express/session')
+ require('express/view')
+
+// --- Core Modules
+
+ use(Express.BodyDecoder)
+ use(Express.MethodOverride)
+ use(Express.ContentLength)
+ use(Express.DefaultContentType)
+ use(Express.RedirectHelpers)
+ use(Express.Mime)
+ use(Express.Cookie)
+ use(Express.Session)
+ use(Express.View)
````