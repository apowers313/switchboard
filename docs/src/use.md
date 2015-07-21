# Use
There are typically two steps to using any service: authorizing and making a call.

## Find a Service
You can find a service that you want to use by browsing through the list of services. Each service includes a description from the contributor, as well as a list of calls and their parameters. The list of services can be found [here](/services.html).

## Authorizing a Service

## Making a Call

## Using the Results

## An Example
The example below shows using the [restify](https://www.npmjs.com/package/restify) client to call the [Google Drive API](https://developers.google.com/drive/v2/reference/). Since Google Drive uses three-legged auth (the most complex scenario for authentication), it requires us to open up a browser so that the user can login to Google Drive to authenticate and allow Switchboard to use the service. For that we use the [open](https://www.npmjs.com/package/open) library.

First we auth

```javascript
var rest = require ('restify');
var open = require ('open');

// Do the authorization call
rest.get ("http://switchboard.center/service/google_drive/auth").on('complete', function (data) {
	if (data.redirect) {
		open (data.redirect); // open the redirect URL in the browser
	}
});
```
At this point, it is up to the user to do what they normally do to authorize in the browser. Assuming that they do the right things, Google Drive will call back to Switchboard with a signal saying that authorization is complete. If we want to wait for that signal to know that authorization was successful, we can use the wait function:

```javascript
rest.get ("http://switchboard.center/service/google_drive/auth/wait").on('complete', function (data) {
	// We don't get here until authorization is done...
});
```
Assuming that authorization completes okay, then we can move on to making our call. In this case we are going to list all of our files in the root directory of our Google Drive account:

```javascript
rest.get ("http://switchboard.center/service/google_drive/list").on('complete', function (data) {
	console.log (data); // Or do something more constructive with the data...
});
```
At this point we get our data printed out to the screen, which should look someting like:

```json
data (TODO)
```
Note that this is much more simple than the data typically returned by Google Drive.

## Common Issues

Well, you can't really have common issues until you have users... having problems? Drop us a note at support@switchboard.center.