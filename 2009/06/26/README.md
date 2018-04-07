# The birth of Express

    git checkout 9998490f93d3ad3d56c00d23c0aa13fac41c3f6b
    
author: visionmedia <tj@vision-media.ca>
    
The initial commit was made on June 26th, 2009 at 11:56:18 AM PDT.

lib/express.core

```javascript
(function(){
  Express = { version : '0.0.1' }
})()

// new node.http.Server(function (req, res) {
//   res.sendHeader(200, [["Content-Type", "text/plain"]])
//   res.sendBody("Hello World")
//   res.finish()
// }).listen(8000)
// 
// puts("Server running at http://localhost:8000/")
//
```

Initial test setup with JSpec.

```javascript
describe 'Express'

end
```

    git checkout 1633662c9b7ed1c505805eed9cf336562d007a0e
    
```javascript
(function(){
  Express = { 
    version : '0.0.1',
    
    /**
     * Start express, binding to _port_.
     *
     * @param {int} port
     * @api public
     */
    
    start : function(port) {
      this.server.listen(port || 3000, function(request, response){
        response.sendHeader(200, [['Content-Type', 'text/plain']])
        response.sendBody('Testing')
        response.finish()
      })
    },
    
    server : {
      
      /**
       * Listen to _port_ with _callback_.
       *
       * @param {int} port
       * @param {function} callback
       * @api private
       */
      
      listen : function(port, callback) {
        new node.http.Server(callback).listen(port)
        puts('Express running at http://localhost:' + port)
      }
    },
    
    /**
     * Return the contents of a function body.
     *
     * @param  {function} body
     * @return {string}
     * @api public
     */
    
    contentsOf : function(body) {
      return body.toString().match(/^[^\{]*{((.*\n*)*)}/m)[1]
    }
  }
  
  Express.start()
})()
```

    get checkout 8848004cb74b82acaa0c558b83ec3b1428c8c59a
    
```javascript

(function(){
  Express = { 
    version : '0.0.1',
    response : { 
      status : 200,
      headers : {},
      statuses : {
        'ok'                 : 200,
        'created'            : 201,
        'accepted'           : 202,
        'no content'         : 204,
        'reset content'      : 205,
        'partial content'    : 206,
        'moved permanently'  : 301,
        'found'              : 302,
        'see other'          : 303,
        'not modified'       : 304,
        'use proxy'          : 305,
        'switch proxy'       : 306,
        'temporary redirect' : 307,
        'bad request'        : 400,
        'unauthorized'       : 401,
        'forbidden'          : 403,
        'not found'          : 404
      }
    },
    
    /**
     * Start express, binding to _port_.
     *
     * @param {int} port
     * @api public
     */
    
    start : function(port) {
      this.server.listen(port || 3000, function(request, response){
        response.sendHeader(200, [['Content-Type', 'text/plain']])
        response.sendBody('Testing')
        response.finish()
      })
    },
    
    server : {
      
      /**
       * Listen to _port_ with _callback_.
       *
       * @param {int} port
       * @param {function} callback
       * @api private
       */
      
      listen : function(port, callback) {
        new node.http.Server(callback).listen(port)
        puts('Express running at http://localhost:' + port)
      }
    },
    
    /**
     * Set response status to _value_. Where value may be
     * a case-insensitive string such as 'Not Found', 'forbidden',
     * or a numeric HTTP response status code.
     *
     * @param  {int, string} value
     * @api public
     */
    
    status : function(value) {
      this.response.status = this.response.statuses[value.toString().toLowerCase()] || value
    },
    
    /**
     * Sets _name_ header to _value_. When _value_
     * is not present the value of the header _name_
     * will be returned (if available).
     *
     * @param {string} name
     * @param {string} value
     * @api public
     */
    
    header : function(name, value) {
      return value ? this.response.headers[name] = value :
               this.response.headers[name]
    },
    
    /**
     * Return the contents of a function body.
     *
     * @param  {function} body
     * @return {string}
     * @api public
     */
    
    contentsOf : function(body) {
      return body.toString().match(/^[^\{]*{((.*\n*)*)}/m)[1]
    }
  }
  
  Express.start()
})()
```

    git checkout 636d261fab93583fb3e2936ab8f1dcc604477a29
    
```javascript
Express.mimeTypes = {
  "3gp"     : "video/3gpp",
  "a"       : "application/octet-stream",
  "ai"      : "application/postscript",
  "aif"     : "audio/x-aiff",
  "aiff"    : "audio/x-aiff",
  "asc"     : "application/pgp-signature",
  "asf"     : "video/x-ms-asf",
  "asm"     : "text/x-asm",
  "asx"     : "video/x-ms-asf",
  "atom"    : "application/atom+xml",
  "au"      : "audio/basic",
  "avi"     : "video/x-msvideo",
  "bat"     : "application/x-msdownload",
  "bin"     : "application/octet-stream",
  "bmp"     : "image/bmp",
  "bz2"     : "application/x-bzip2",
  "c"       : "text/x-c",
  "cab"     : "application/vnd.ms-cab-compressed",
  "cc"      : "text/x-c",
  "chm"     : "application/vnd.ms-htmlhelp",
  "class"   : "application/octet-stream",
  "com"     : "application/x-msdownload",
  "conf"    : "text/plain",
  "cpp"     : "text/x-c",
  "crt"     : "application/x-x509-ca-cert",
  "css"     : "text/css",
  "csv"     : "text/csv",
  "cxx"     : "text/x-c",
  "deb"     : "application/x-debian-package",
  "der"     : "application/x-x509-ca-cert",
  "diff"    : "text/x-diff",
  "djv"     : "image/vnd.djvu",
  "djvu"    : "image/vnd.djvu",
  "dll"     : "application/x-msdownload",
  "dmg"     : "application/octet-stream",
  "doc"     : "application/msword",
  "dot"     : "application/msword",
  "dtd"     : "application/xml-dtd",
  "dvi"     : "application/x-dvi",
  "ear"     : "application/java-archive",
  "eml"     : "message/rfc822",
  "eps"     : "application/postscript",
  "exe"     : "application/x-msdownload",
  "f"       : "text/x-fortran",
  "f77"     : "text/x-fortran",
  "f90"     : "text/x-fortran",
  "flv"     : "video/x-flv",
  "for"     : "text/x-fortran",
  "gem"     : "application/octet-stream",
  "gemspec" : "text/x-script.ruby",
  "gif"     : "image/gif",
  "gz"      : "application/x-gzip",
  "h"       : "text/x-c",
  "hh"      : "text/x-c",
  "htm"     : "text/html",
  "html"    : "text/html",
  "ico"     : "image/vnd.microsoft.icon",
  "ics"     : "text/calendar",
  "ifb"     : "text/calendar",
  "iso"     : "application/octet-stream",
  "jar"     : "application/java-archive",
  "java"    : "text/x-java-source",
  "jnlp"    : "application/x-java-jnlp-file",
  "jpeg"    : "image/jpeg",
  "jpg"     : "image/jpeg",
  "js"      : "application/javascript",
  "json"    : "application/json",
  "log"     : "text/plain",
  "m3u"     : "audio/x-mpegurl",
  "m4v"     : "video/mp4",
  "man"     : "text/troff",
  "mathml"  : "application/mathml+xml",
  "mbox"    : "application/mbox",
  "mdoc"    : "text/troff",
  "me"      : "text/troff",
  "mid"     : "audio/midi",
  "midi"    : "audio/midi",
  "mime"    : "message/rfc822",
  "mml"     : "application/mathml+xml",
  "mng"     : "video/x-mng",
  "mov"     : "video/quicktime",
  "mp3"     : "audio/mpeg",
  "mp4"     : "video/mp4",
  "mp4v"    : "video/mp4",
  "mpeg"    : "video/mpeg",
  "mpg"     : "video/mpeg",
  "ms"      : "text/troff",
  "msi"     : "application/x-msdownload",
  "odp"     : "application/vnd.oasis.opendocument.presentation",
  "ods"     : "application/vnd.oasis.opendocument.spreadsheet",
  "odt"     : "application/vnd.oasis.opendocument.text",
  "ogg"     : "application/ogg",
  "p"       : "text/x-pascal",
  "pas"     : "text/x-pascal",
  "pbm"     : "image/x-portable-bitmap",
  "pdf"     : "application/pdf",
  "pem"     : "application/x-x509-ca-cert",
  "pgm"     : "image/x-portable-graymap",
  "pgp"     : "application/pgp-encrypted",
  "pkg"     : "application/octet-stream",
  "pl"      : "text/x-script.perl",
  "pm"      : "text/x-script.perl-module",
  "png"     : "image/png",
  "pnm"     : "image/x-portable-anymap",
  "ppm"     : "image/x-portable-pixmap",
  "pps"     : "application/vnd.ms-powerpoint",
  "ppt"     : "application/vnd.ms-powerpoint",
  "ps"      : "application/postscript",
  "psd"     : "image/vnd.adobe.photoshop",
  "py"      : "text/x-script.python",
  "qt"      : "video/quicktime",
  "ra"      : "audio/x-pn-realaudio",
  "rake"    : "text/x-script.ruby",
  "ram"     : "audio/x-pn-realaudio",
  "rar"     : "application/x-rar-compressed",
  "rb"      : "text/x-script.ruby",
  "rdf"     : "application/rdf+xml",
  "roff"    : "text/troff",
  "rpm"     : "application/x-redhat-package-manager",
  "rss"     : "application/rss+xml",
  "rtf"     : "application/rtf",
  "ru"      : "text/x-script.ruby",
  "s"       : "text/x-asm",
  "sgm"     : "text/sgml",
  "sgml"    : "text/sgml",
  "sh"      : "application/x-sh",
  "sig"     : "application/pgp-signature",
  "snd"     : "audio/basic",
  "so"      : "application/octet-stream",
  "svg"     : "image/svg+xml",
  "svgz"    : "image/svg+xml",
  "swf"     : "application/x-shockwave-flash",
  "t"       : "text/troff",
  "tar"     : "application/x-tar",
  "tbz"     : "application/x-bzip-compressed-tar",
  "tcl"     : "application/x-tcl",
  "tex"     : "application/x-tex",
  "texi"    : "application/x-texinfo",
  "texinfo" : "application/x-texinfo",
  "text"    : "text/plain",
  "tif"     : "image/tiff",
  "tiff"    : "image/tiff",
  "torrent" : "application/x-bittorrent",
  "tr"      : "text/troff",
  "txt"     : "text/plain",
  "vcf"     : "text/x-vcard",
  "vcs"     : "text/x-vcalendar",
  "vrml"    : "model/vrml",
  "war"     : "application/java-archive",
  "wav"     : "audio/x-wav",
  "wma"     : "audio/x-ms-wma",
  "wmv"     : "video/x-ms-wmv",
  "wmx"     : "video/x-ms-wmx",
  "wrl"     : "model/vrml",
  "wsdl"    : "application/wsdl+xml",
  "xbm"     : "image/x-xbitmap",
  "xhtml"   : "application/xhtml+xml",
  "xls"     : "application/vnd.ms-excel",
  "xml"     : "application/xml",
  "xpm"     : "image/x-xpixmap",
  "xsl"     : "application/xml",
  "xslt"    : "application/xslt+xml",
  "yaml"    : "text/yaml",
  "yml"     : "text/yaml",
  "zip"     : "application/zip"
}
```

...

# References
[JSpec](https://github.com/liblime/jspec) 