# Use
There are typically two steps to using any service: authorizing and making a call.

## Find a Service
You can find a service that you want to use by browsing through the list of services. Each service includes a description from the contributor, as well as a list of calls and their parameters. The list of services can be found [here](/services.html).

## Authorizing a Service
Authorizing with a service is as simple as calling: `http://switchboard.center/service/<service_name>/auth`, such as `http://switchboard.center/service/google_drive/auth` for Google Drive.

Some services will require "three-legged" authentication. That's where you have to login to the service and approve that Switchboard can access the service. If a service is using three-legged authentication, it will return some JSON that looks like:
``` json
{ 
	"success": "true",
	"result": "token",
	"err": "null",
	"msg": "Authentication successful",
	"redirect": "http://switchboard.center/api/service/google_drive/callback/redirect?redirect=https%3A%2F%2Faccounts.google.com%2Fo%2Foauth2%2Fauth%3Fredirect_uri%3Dhttp&waitId=d613d8c4b807146f696986785624c6ba",
	"browserRedirect": "http://switchboard.center/api/service/google_drive/callback/redirect?redirect=https%3A%2F%2Faccounts.google.com%2Fo%2Foauth2%2Fauth%3Fredirect_uri%3Dhttp&waitId=d613d8c4b807146f696986785624c6ba",
	"waitUrl": "http://switchboard.center/api/service/google_drive/callback/wait/d613d8c4b807146f696986785624c6ba" 
}
```

The `redirect` and `browserRedirect` URLs will be the same -- they both indicate that a browser should be used to finialze the authentication. This requires that the process requesting authentication wait until authentication is done before making a call. There are two ways to wait for authentication to complete: synchronous and asynchronous.

The synchronous method uses the `waitUrl` that is returned by the authentication call. Calling this URL will block until the authentication has finished or until the wait call times out. By default the wait call will timeout after 90 seconds, but this can be adjusted by adding `?timeout=seconds` to the end of the URL. For example, `http://switchboard.center/api/service/google_drive/callback/wait/d613d8c4b807146f696986785624c6ba?timeout=30` would timeout after 30 seconds instead of 90.

The asynchronous method uses `resultRedirect` to callback to a URL once the browser is done. Traditionally, this requires that the client listen to a port and accept the input to know that the call has completed and it is up to the client to time out if something goes wrong. On mobile devices, it is possible that the `resultRedirect` could be a custom URL scheme that triggers an [Android intent](https://developer.chrome.com/multidevice/android/intents) ([see also](http://developer.android.com/reference/android/content/Intent.html)) or a [iOS URL Scheme](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html) (for example to close a Web View). (Note: these haven't been tested yet, but in theory they should work...)

## Making a Call
Making a call is as easy as calling the URL for the call: `http://switchboard.center/service/<service_name>/<call_name>`, such as `http://switchboard.center/service/google_drive/list`. If the call takes parameters, they can be added using a [query string](https://en.wikipedia.org/wiki/Query_string). For example, if the call takes a `fileId` parameter, then you can set the value of `fileId` by calling `http://switchboard.center/service/google_drive/get?fileId=abc123` which would set the value of `fileId` to `abc123`.

## Using the Results
All results from a call will be in JSON format and will only return the fields that have been selected by the service.

## An Example
The example below shows using the [restler](https://www.npmjs.com/package/restler) client to call the [Google Drive API](https://developers.google.com/drive/v2/reference/). Since Google Drive uses three-legged auth (one of the more complex scenarios for authentication), it requires us to open up a browser so that the user can login to Google Drive to authenticate and allow Switchboard to use the service. For that we use the [open](https://www.npmjs.com/package/open) library.

First we auth

```javascript
var rest = require ('restler');
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
rest.get ("http://switchboard.center/api/service/google_drive/callback/wait/d613d8c4b807146f696986785624c6ba").on('complete', function (data) {
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