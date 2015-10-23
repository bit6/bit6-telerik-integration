# Bit6 with Telerik Backend Services

Telerik Backened Services provide a great support for user management. With Bit6 you can easily enable voice and video calling as well as rich media messaging between your users, while delegating the user authentication to Telerik.

### Prerequisites

1. Create an app in Telerik
2. Build your web or mobile app that authenticates users with the Telerik Backend Services
3. Create an app in [Bit6 Dashboard](https://dashboard.bit6.com) - you will need app API key and secret

### Cloud Function

1. Follow [Telerik docs](http://docs.telerik.com/platform/backend-services/javascript/server-side-logic/cloud-code/cloud-functions/cloud-functions-getting-started) to get started with Cloud Functions
2. Create an empty `bit6_auth` cloud function in Telerik Business Logic -> Explorer
3. Upload `cloud/bit6_auth.js` file from this repo into your project as `bit6_auth.js` file
4. Set your Bit6 app API key and secret in the body of the `bit6_auth` function in that file

### App Integration
In the client-side app code the integration consists of the same steps for all the platforms:

1. Initialize Bit6 SDK with your API key
2. Follow the Telerik documentation to authenticate the user
3. Execute `bit6_auth` cloud function to generate the Bit6 "external token"
4. Provide this token as an argument to `bit6.session.external(token)` method
5. Your Telerik user is now authenitcated with Bit6 and can be reached by his Telerik userId, username, email or other identities specified in the cloud function

### Cordova or Web App - JavaScript / CoffeeScript

* Live web demo: http://demo.bit6.com
* Cordova sample app: https://github.com/bit6/bit6-cordova-demo-telerik-backend

Sample JavaScript code:
```js
// Init Telerik SDK
var el = new Everlive({apiKey: 'MyTelerikApiKey', scheme: 'https'});
// Init Bit6 SDK
var bx = new bit6.Client({apikey: 'MyBit6ApiKey'});
// Login as a Telerik user 'alex' with password 'abc'
el.authentication.login('alex', 'abc',
  // Login successful
  function(data) {
    // Generate Bit6 external token in the cloud function
    var opts = {
      endpoint: 'functions/bit6_auth',
      // Got the token
      success: function(a) {
        var token = a.result;
        // Use the token to authenticate with Bit6
        if (token) {
          bx.session.external(token, function(err) {
            // Done! Now you can use all the Bit6 communication APIs.
            // For example, send a message to a Parse user with username 'john'
            bx.compose('usr:john').text('Yo!').send();
          });
        }
      }
    };
    // Call the cloud function to get the token
    el.request(opts).send();
  },
  // Login failed
  function(err) {
  }
);
```

Sample CoffeeScript code:
```coffee
# Init Telerik SDK
el = new Everlive {apiKey: 'MyTelerikApiKey', scheme: 'https'}
# Init Bit6 SDK
bx = new bit6.Client {apikey: 'MyBit6ApiKey'}
# Login as a Telerik user 'alex' with password 'abc'
el.authentication.login 'alex', 'abc', (data) ->
    # Generate Bit6 external token in the cloud function
    opts =
      endpoint: 'functions/bit6_auth'
      # Got the token
      success: (a) ->
        token = a.result
        # Use the token to authenticate with Bit6
        if token
          bx.session.external token, (err) ->
            # Done! Now you can use all the Bit6 communication APIs.
            # For example, send a message to a Parse user with username 'john'
            bx.compose('usr:john').text('Yo!').send()
    # Call the cloud function to get the token
    el.request(opts).send()
  # Login failed
  , (err) ->
```
