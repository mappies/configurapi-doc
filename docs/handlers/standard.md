# Standard Handlers

## Using standard handlers

Configurapi comes with standard handlers.  To use it, simply install the desired package using npm and reference it in the *import* section of the application's configuration file. 


## HTTP


**Source**: [GitHub](https://github.com/mappies/configurapi-handler-http)

**Package**: [configurapi-handler-http](https://www.npmjs.com/package/configurapi-handler-http)

<a name="setResponseBodyHandler"></a>
&nbsp;
### setResponseBodyHandler 

Set the response's body of an event.

##### Parameters

 1. *string* - Response body

##### Example

    - setResponseBodyHandler:
      - Hello world

<a name="setResponseHandler"></a>
&nbsp;
### setResponseHandler

Set the response of an event.

##### Parameters
 
 1.  *number* - Status code
 1.  *string* - Response body
 1.  *object* - A key-value pair representing headers (optional)

##### Example

    - setResponseHandler:
      - 200
      - Hello world
      - {Content-Type: plain/text, X-Header: value}

<a name="setResponseHeaderHandler"></a>
&nbsp;
### setResponseHeaderHandler

Set response's headers.

##### Parameters

 1. *object* - Header name
 1. *string* - Header value

##### Example
    
    - setResponseHeaderHandler:
      - Content-Type
      - plain/text

<a name="setResponseStatusCodeHandler"></a>
&nbsp;
### setResponseStatusCodeHandler

Set response's status code.

##### Parameters

 1. *number* - Status code

##### Example
    
    - setResponseStatusCodeHandler:
      - 404

## JSON

**Source**: [GitHub](https://github.com/mappies/configurapi-handler-json)

**Package**: [configurapi-handler-json](https://www.npmjs.com/package/configurapi-handler-json)

<a name="setJsonResponseHandler"></a>
&nbsp;
### setJsonResponseHandler

Set the response of an event in JSON format.  The content-type will be set to application/json.

##### Parameters
 
 1.  *number* - Status code
 1.  *string* - Response body
 1.  *object* - A key-value pair representing headers (optional)

##### Example

    - setJsonResponseHandler:
      - 200
      - {hello: world}
      - {Content-Type: plain/text, X-Header: value}


<a name='validateJsonRequestHandler'></a>
&nbsp;
### validateJsonRequestHandler 

Validate incoming request using JSON schema. By default, the handler will look for a schema file at *schemas/event_name.json*

##### Parameters
 
 1. *string* - *emptyPayload* if no request payloads are expected (optional)

##### Example

    - validateJsonRequestHandler

&nbsp;

&nbsp;
