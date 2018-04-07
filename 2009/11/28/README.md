# All tests pass now

    git checkout bac22ca3bc69d0b0f8d34668066535e3e4fe8148
   
```diff
   
   describe 'BodyDecoder'
     it 'should parse urlencoded bodies'
-      request = mockRequest({ 
+      request = mockRequest({
+          method: 'POST',
           headers : [['Content-Type', 'application/x-www-form-urlencoded']],
           body : 'user[name]=foo%20bar&status=1'
         })

```

```diff
     end
 
     it 'should parse JSON bodies'
-      request = mockRequest({ 
+      request = mockRequest({
+          method: 'POST',
           headers : [['Content-Type', 'application/json']],
           body : '{ foo : "bar" }'
         })

```

```diff
   describe 'MethodOverride'
     it 'should override request method when _method param is present'
-      request = mockRequest({ 
+      request = mockRequest({
+          method: 'POST',
           headers : [['Content-Type', 'application/x-www-form-urlencoded']],
           body : '_method=delete'
         })
       del('foo', function(){ 'Deleted' })
-      post('foo', { request : { body : '_method=delete' }}).body.should.eql 'Deleted'
+      post('foo', { request : request}).body.should.eql 'Deleted'
     end
   end

```