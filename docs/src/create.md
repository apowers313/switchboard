# Create
One of the core ideas of Switchboard is enabling anyone to add a service. Hopefully this means that smaller services and new services can get some love, and users aren't just locked into a few big services.

To that end, we have made creating a service as easy as possible: just enter the calls and parameters that your service needs and everyone can start using it. If your service requires authentication, such as three-legged OAuth2, we have a number of pre-defined authentication types -- just drop in the URLs and keys needed for authentication, and Switchboard will take care of the rest of the protocol complexities and storing the keys to be used with the calls.

Currently, all the work done to define a new service is done using [Postman](https://www.getpostman.com) (although, maybe it will all be done in the browser someday soon). After defining the calls and paramters in Postman, just upload the files to the Switchboard website, set a few settings, and viola! A new service is born... Don't worry, we walk through all the steps below. And if you need any help, feel free to fire up Gitter and ask away.

>**A Special Note on Design:**
>
>The goal of Switchboard is to make services easier to use. As you are creating your services, keep this in mind. Each call and parameter that you create should be clear and concise. While many APIs may have lots of options, bells and whistles, most of the time they won't be used. The style of Switchboard is to focus on enabling specific use cases, potentially even duplicating calls to the same APIs for different use cases. Each use case should have the minimal number of paramaters needed to complete the use case.
>
>For example, in the Google Drive service, the underlying Google APIs have lots of paramters, but our `List` call doesn't require any parameters and our `Get` call only requires a single `FileID`.
>
>Before getting started, give some thought to how you expect people to use the service. What are the minimum number of calls and paramters that they would need? If they need all the bells and whistles of the full API, you can always add that later, but for now maybe you should just make things easy for them...

## Using Postman

The majority of the work for creating a new service is just creating the API calls the way that they are normally created in Postman. Lots of engineers use Postman for testing out API calls -- one of the reasons why Switchboard relies on Postman -- so hopefully it is a familiar tool. If not, don't worry -- Postman is pretty easy to use and they have great [documentation](https://www.getpostman.com/docs) and [support](https://www.getpostman.com/support).

In the sections below we will create four things:
* **Service**: This is the overall thing you are trying to create -- a set of API calls to some existing web service. Postman refers to this as a "collection".
* **Calls**: These are the GET and POST calls to specific URLs that make up your service. Postman refers to these as requests.
* **Parameters**: These are the variables for each call. Maybe a File ID or a username a date or a number, they are the things you will want to enable your users to set for each call. Postman refers to these as variables.
* **Returns**: The result of a call is a return. Switchboard will normalize all returns to JSON, parse out just the fields defined by your service, and pass back the type and value. Postman's name for returns is responses.

In order to build out these pieces, first we will have to authenticate with the service...

### Authenticating

In order to test your API calls in Postman you will have to enter in the authentication information for the call. Note that this authentication information is not saved as part of the Postman file and will have to be re-added again as part of the settings after uploading the Postman file. Don't worry, we cover that later, but in the meantime Postman has some good documetnation on their [Authentication Helpers](https://www.getpostman.com/docs/helpers) to get you to the point where you can make your calls. When your authentication is setup, you are ready to start making calls to the service.

### Creating and Testing Parameters

After setting up any necessary authentication, you can start making calls to the service. [Postman has great documentation](https://www.getpostman.com/docs/requests) that decribes how to setup new requests, including the URL of the request, the parameters, the headers and a body (if a body is required). 

We won't rehash how to make Postman requests here, but we do want to focus on one advanced feature of Postman that we will make use of: [variables](https://www.getpostman.com/docs/environments). Each variable defined in Postman becomes a parameter to a Switchboard call. Postman variables can be defined anywhere: in the URL, the URL parameters, the header, or the body. Simply create a string that looks like: {{variableName}}, where anything between two sets of curley braces is a variable. 

![Postman Variable](/images/IMG_0897.PNG)
*This image shows a Postman Variable in the URL string. The variable has been highlighted in blue.*

Postman will use the Postman Environment to substitute values for each variable, so that you can test out your requests and variables. The environment settings are in the top right-hand corner of Postman.

<img src="/images/IMG_0898.PNG" height="350px"><br />
*This image shows where to find the Postman Environment Settings.*

Creating all your calls and parameters is as easy as creating all the Postman requests and variables. You should focus on minimizing the number of calls and variables to a minimum -- the purpose here isn't to duplicate the whole API of a service, but rather to simplify it.

Don't forget to save your calls to a collection as you create them. In the next step we will use the descriptions of the saved calls and collections to make them more useful.

### Describing Paramters and Return Values

After creating all the parameters and return values for your service, you will probably want to describe them so that people know what to do with them. Describing parameters uses the description field in Postman. We have created a notation similar to [JavaDoc](http://www.oracle.com/technetwork/java/javase/documentation/index-137868.html#format), which we call PostDoc. 

![An example of a PostDoc description](/images/IMG_0900.PNG =250x)
*An example of a PostDoc description in a Postman description field.*

This notation uses the `@param` keyword to describe parameters. The most general form of a parameter is `@param name type description`. Let's look at each one of those pieces:

* **@param**: Start the line with @param to let PostDoc know that you are describing a parameter in this line.
* **name**: The name of the parameter. This should match up with the variables in your Postman requests, so if you have a `{{foo}}` variable you should have a `@param foo` description. Note that you can also wrap brackets around a name to make it optional, so `@param [foo]` would be an optional parameter named `foo`.
* **type**: What is the type of this thing? Options include: string, number, date, URL, email, etc. You can also have a list of any one of those as a type, such as a ListOfString. Note that you can use ListOf or ArrayOf before any type to declare it as a list, or you can put \[\] afterwards. ListOfNumber, ArrayOfNumber and Number[] are all the same.
* **description**: A text description of this paramter, which lasts until the end of the line. This gets shown to people as part of the documentation of the service.

In addition to defining your parameters, you can also define what the call returns using the `@returns` keyword. Each call can return multiple values. The format of the return call is `@return name type [path] description`. You will note that this looks the same as the `@param` statement, but it has a path value too. The path description is a [JSON Path](https://www.npmjs.com/package/JSONPath) statement for parsing out the return value from the data returned by the service. JSON Path is to JSON what XPath is to XML, and since Switchboard converts all the return values to JSON, it's a convenient way of cherry picking the results to return.

Anything in the description field that doesn't start with the @ symbol is passed through as the description of the call, and is shown to people as part of the documentation of the service.

### Exporting Postman Files
At this point, you should have created and tested all of your calls and parameters and created descriptions of the parameters and returns. One last step is to add a description of the service before exporting the service from Postman. This description will be used to describe your service to people, so make sure that you do a good job of describing it!

Finally, export your service from Postman. This is as simple as selecting the three dots next to your collection in Postman and selecting `Download`. 

<img src="/images/IMG_0899.PNG" height="350px"><br />
*This image shows how to export your service from Postman*

Exporting your service creates a JSON file that you will later upload to Switchboard.

## Uploading and Configuring Services

## An Example: Creating a Google Drive Service

1. Find the developer documentation
2. Configure the service
3. Decide which calls to use
4. Create a new Postman collection
5. Create your first call: List
6. Create your second call: Get
7. Add a parameter
8. Describe your calls, parameters and returns
9. Export your service
10. Upload service
11. Set your authentication parameters
12. Done!

## PostDoc

### Paramters

### Returns

### Types

### PEG Grammar Description

## Coming Soon...

### Defining and sharing types

### Templates