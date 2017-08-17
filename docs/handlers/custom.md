
You can add your own handlers to process complex logics.  In this section, we will learn how to add your own custom handlers.

## Adding your own handlers

A handler is a function defined in a JavaScript or TypeScript file.  The file must be located in *handlers* directory.

    mkdir handlers

Let's create a simple 'Hello World" handler by creating a handler file called *handlers/helloWorldHandler.js* with the following contents:

    module.exports = function helloWorldHandler(event)
    {
        event.response.body = "Hello, World";
    };

In this handler, we simply set the response body to "Hello, World".

Then, update the config.yaml file to use our new handler.

    api:
      events:
        - name: ""
          policies:
          - helloWorldHandler

Let's run configurapi:

    configurapi-runner-self -p 8080

and test it:

    curl http://localhost:8080/
    Hello, World

## Response

So far, you have learned how to set the content of a response.  

Each event has a response.  A handler processes an incoming event and generates an appropiate response for the caller.  

Configurapi provides four types of responses -- *Response*, *JsonResponse*, *ListResponse*, and *ErrorResponse*

<a name="Response"></a>
&nbsp;
### Response
**Package**: [configurapi](https://www.npmjs.com/package/configurapi)

A raw response object.  This is the default type of response that is initialized when an event is created.


**Constructor Parameters**
 
 * statusCode: *number* - The request's status code. (Default: 200)
 * body: *any* - The request's body.
 * headers: *{}* Key-value pairs representing the response's handers. (Default: {})

**Properties**

 * body: *string* - The response's body. 
 * statusCode: *number* - The response's status code.
 * headers: *object* - Key-value pairs representing the response's handers. 

**Example**

    module.exports = function helloWorldHandler(event)
    {
        event.response.statusCode = 202;
        event.response.body       = {hello: "World"};
        event.response.headers    = {"Content-Type": "application/json"};
    };


<a name="JsonResponse"></a>
&nbsp;
### JsonResponse
**Package**: [configurapi-handler-json](https://www.npmjs.com/package/configurapi-handler-json)

A JSON response object.  This is the same as [Response](#Response) above but it ensures that the Content-Type is appropiately set for JSON.

**Constructor Parameters**
 
 * body: *any* - The request's body.
 * statusCode: *number* - The request's status code. (Default: 200)
 * headers: *{}* Key-value pairs representing the response's handers. (Default: {})

**Example**

    const JsonResponse = require('configurapi-handler-json').JsonResponse;

    module.exports = function helloWorldHandler(event)
    {
        event.response = new JsonResponse({hello: "world"});
    };

<a name="ListResponse"></a>
&nbsp;
### ListResponse
**Package**: [configurapi-handler-json](https://www.npmjs.com/package/configurapi-handler-json)

A response object representing a list of items. *start* and *total* are used for index-based paginations.

**Constructor Parameters**
 
 * collection: *array* - A list of items to include in this response.
 * start: *number* - The start index of the items. (Default: 0)
 * total: *number* - The total number of items in the entire collection. (Default: collection.length)
 * statusCode: *number* - The request's status code. (Default: 200)
 * headers: *{}* Key-value pairs representing the response's handers. (Default: {})

**Example**

    const ListResponse = require('configurapi-handler-json').ListResponse;

    module.exports = function helloWorldHandler(event)
    {
            event.response = new ListResponse(["Hello", "World"]);
    };


<a name="ErrorResponse"></a>
&nbsp;
### ErrorResponse
**Package**: [configurapi](https://www.npmjs.com/package/configurapi)

A response object representing an error. This type of response is returned when an error occurs in configurapi. 

**Constructor Parameters**
 
 * error: *string* - The error message.
 * statusCode: *number* - The request's status code. (Default: 500)
 * details: *string* The error details such as a stack trace. (Default: '')

**Example**

    const ErrorResponse = require('configurapi').ErrorResponse;

    module.exports = function helloWorldHandler(event)
    {
            try
            {
                    throw new Error("Could not say hello!");
            }
            catch(e)
            {
                    event.response = new ErrorResponse(e);
            }
    };


## Synchronous handlers

Synchronous handlers are handlers that return responses synchronously.  The following handler shows an example of a synchromous handler that returns a simple sum result synchronously.
 
```
    module.exports = function addHandler(value1, value2)
    {
        event.response.body = value1 + value2;
    };
```

A synchronous handler is the simplest form of a handler.  However, it does not support a callback, a promise, or async/await.  In that case, an asynchronous handler must be used instead.

## Asynchronous handlers

Asynchronous handlers are handlers that support asynchronous programming paradigm.  In asynchronous handlers, callbacks, promises, and async/await can be used to improve performance of the application. 

### Callback

For a handler that uses a callback,  **this.continue()** or **this.complete()** must be called to indicate the completion of the handler. The only difference between **this.continue()** and **this.complete()** is **this.continue()** allows subsequent handlers to be processed while **this.complete()** indicates the completion of the entire request and returns the response back to the caller immediately.

The following example shows how **this.continue()** and **this.complete()** could be used to indicate the completion of a handler using a callback.


    const ErrorResponse = require('configurapi').ErrorResponse;

    function divide(value1, value2, callback)
    {
        if(value2 == 0)
        {
            callback(new Error("Divide by zero"));
        }
        else
        {
            callback(undefined, value1/value2);
        }
    }

    module.exports = function divideCallbackHandler(event, value1, value2)
    {
        divide(value1, value2, (error, result) => {

                if(error)
                {
                    event.response = new ErrorResponse(error);
                    this.complete();
                }
                else
                {
                    event.response.body = result.toString();
                    this.continue();
                }
        });
    };


### Promise

A handler can return a promise.  Calling the promise's **resolve()** and **reject()** normally to indicate the completion of the handler's execution.

The following is an example of a handler that returns a promise.

```
    module.exports = function dividePromiseHandler(event, value1, value2)
    {
        return new Promise((resolve, reject) => {

            if(value2 === 0)
            {
                reject(new Error("Divide by zero"));
            }
            else
            {
                event.response.body = (value1 / value2).toString();
                resolve();
            }
        });
    };
```

### Async/Await

```
    async function addAsync(value1, value2)
    {
        return new Promise((resolve, reject) => {
            setTimeout(() => resolve(value1+value2), 10);
        };
    }

    module.exports = async function addHandler(event, value1, value2)
    {
        let result = await addAsync(value1, value2);
        event.response.body = result.toString();
    };

```