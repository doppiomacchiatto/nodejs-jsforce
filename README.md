# node-jsforce
This application was built to test out the [JSforce](https://jsforce.github.io/) js library.  JSforce was designed specifically for Salesforce.com.

This application uses node.js 4.x, which was great to review and become familiar with the new functionality.  The last time that I built a node.js app, I used node.js 3.x.  There are plenty of free content that highlight the key differences from  node.js 3.x to 4.x.

##Oauth 2
The application uses the  Oauth 2 specification.  Make sure that you read the [JSforce](https://jsforce.github.io/document/). I defined an oauth variable with the parameters required to establish a connection.  I did not use the Client Secret.  In this case, I am implementing the [User-Agent Flow](https://help.salesforce.com/apex/HTViewHelpDoc?id=remoteaccess_oauth_user_agent_flow.htm&language=en_US). This pattern is effective when a device can't keep the client secret confidential.  The token is passed as part of an HTTP re-direction.

The code snippet below shows how the route parses the token that's return as part of HTTP re-direct.

Node.js new functionality is how to access the url paramter.  In this case, SFDC sets the code=<token>.  Notice how with node.js 4.x you can get the response params with dot notation.

```
router.get('/token', function(req, res) {
        console.log('token is'+req.query.code);
  var conn = new jsforce.Connection({ oauth2 : oauth2 });
//deprecated req.param(name): Use req.params, req.body, or req.query instead 
  var code = req.query.code;
  conn.authorize(code, function(err, userInfo) {
    if (err) { return console.error(err); }
})})
```
## Pre-Requesites
1. Sign-up for a developer SFDC Org.
2. Create a Connected App
..* Capture the ClientId.
..* Make sure that you map the loopback URL to your application. (http://localhost:port/token).  In this case the loopback URL points back to a node.js route, which handles the callback and the token.

3. Modify the app.js file.  

Here's a snippet of the app.js file.  
```
var oauth2 = new jsforce.OAuth2({
    // you can change loginUrl to connect to sandbox or prerelease env.
    loginUrl : '<Your org>',
    clientId : '<Your clientId>',
    clientSecret : '242430930269556811',
    redirectUri : 'http://localhost:' + port +'/token'
});
app.locals.title = 'sfdc app';
app.locals.emails = '<myemail@gmail.com>';
```
