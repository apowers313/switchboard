# Create

## Overview
Use Postman to create the URLs for services
Add {{parameters}} to Postman
Create environments to test {{parameters}}
Document parameters, types and returns using PostDoc
Export Postman file
Upload Postman file
Configure service Auth settings

## Table of Contents
[TOC]

## About Postman
This section really has nothing to do with our service -- it's all part of Postman. But to make it easier, we rehash some of the key Postman features here.

Get and Install
https://www.getpostman.com/

Create calls
Headers, parameters, bodies
Note: think about what calls and parameters you really want. One of the things worth thinking about is having very simple calls with very few parameters.

Parameters

Environments

## PostDoc
PostDoc is our custom documentation format for Postman. It is like JavaDoc, but it does a bit more than documentation -- it also defines types and parses return values, which makes it easier to define services.

### Calls

### Parameters

### Returns

### Types
NOT IMPLEMENTED YET -- COMING SOON

### Imports
NOT IMPLEMENTED YET -- COMING SOON

# Create

> A special note on design:
> The goal of Switchboard is to make services easier to use. As you are creating your services, keep this in mind. Each call and parameter that you create should be clear and concise. While many APIs may have lots of options, bells and whistles, most of the time they won't be used. The style of Switchboard is to focus on enabling specific use cases, potentially even duplicating calls to the same APIs for different use cases. Each use case should have the minimal number of paramaters needed to complete the use case.
>
> For example, in the Google Drive service, the underlying Google APIs have lots of paramters, but our `List` call doesn't require any parameters and our `Get` call only requires a single `FileID`.

## Using Postman

The majority of the work for creating a new service is just creating the API calls the way that they are normally created in Postman.

### Authenticating

In order to test your API calls in Postman you will have to enter in the authentication information for the call. Note that this authentication information is not saved as part of the Postman file and 

### Creating and Testing Parameters

### Describing Paramters and Return Values
