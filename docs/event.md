# Event

Since Configurapi is an event-based framework, an incoming request will be converted to an event.  In this section, we will discuss 4 important event data -- name, parameters, payload, and response.

### Event name

An event has a name.  In API applications, an event name is auto-generated from an API route using the following formular:

    method_version?_collection1_collection2_..._collectionN

#### Method

A method is basically a HTTP method with an exception of GET which could be either 'get' or 'list' depending on its context.

Example:

HTTP Request    | Method
----------------|--------------
GET /users      | list
GET /users/{id} | get
POST /users     | post
PUT /users      | put
PATCH /users    | patch
DELETE /delete  | delete

#### Version

API Versioning is optional.  If exists, it must be at the left most of the path and started with 'v' followed by digits.

Examples: 

HTTP Request     | Version
-----------------|-------------- 
POST /users      | 
POST /v1/users   | v1
POST /v2/users   | v2
POST /v250/users | v250

#### Collection

A collection is a group of resources based on RESTful API design principal.  An API route may have zero or more collections. 

Examples: 

HTTP Request                             | Collection(s)
-----------------------------------------|-------------- 
GET /                                    |
GET /users                               | users
GET /users/someone                       | users
GET /users/someone/phones                | users, phones
GET /users/someone/phones/123            | users, phones
GET /users/someone/phones/123/tags       | users, phones, tags
GET /users/someone/phones/123/tags/home  | users, phones, tags

#### Event Name Examples

So, with *method*, *version*, and *collection*, we can easily translate an HTTP request to an event name. The following table demonstrates examples of URL to event name translations.  Notice also how configurapi singularizes container names.

HTTP Request                               | Event name
-------------------------------------------|-------------- 
GET /users                                 | list_users
GET /users/someone                         | get_user
GET /users/someone/phones                  | list_user_phones
GET /users/someone/phones/123              | get_user_phone
GET /users/someone/phones/123/tags         | list_user_phone_tags
GET /users/someone/phones/123/tags/home    | get_user_phone_tag
GET /v1/users/someone                      | get_v1_user
GET /v1/users/someone/phones/123/tags/home | get_v1_user_phone_tag
POST /v1/users/                            | post_v1_user
PUT /v1/users/someone                      | put_v1_user
PATCH /v2/users/someone/phones/123         | patch_v2_user_phone
DELETE /users/someone/phones/123           | delete_user_phone



## Event Parameters

An event parameters are a key-value pair containing URL data that are not captured by its event name.  Examples of such data are containers' identifiers and query strings.  Here are some examples of event parameters. Notice also how configurapi singularizes parameter names.
 

HTTP Request                             | Parameter(s)
-----------------------------------------|-------------- 
GET /users                               | {}
GET /users/someone                       | {user: "someone"}
GET /users/someone/phones                | {user: "someone"}
GET /users/someone/phones/123            | {user: "someone", phone: "123"}
GET /users/someone/phones/123/tags       | {user: "someone", phone: "123"}
GET /users/someone/phones/1234/tags/home | {user: "someone", phone: "123", tag: "home"}
GET /users?q1=v1&q2=v2                   | {q1: "v1", q2: "v2"}
GET /users/someone/phones?q1=v1&q2=v2    | {user: "someone", q1: "v1", q2: "v2"}


## Event Payload

An event payload is basically a serialized request's payload based on its specified content type.


HTTP Request Payload      | HTTP Content-Type Header    | Event Payload
--------------------------|-----------------------------|-------------- 
This is a string.         | plain/text                  | "This is a string."
{"username": "someone"}   | plain/text                  | "{\"username\": \"someone\"}"
{"username": "someone"}   | application/json            | {username: "someone"}

## Event Response

After an event has been processed, a response should be returned to the caller.  We can set an event's response either through configurations (e.g. [setResponseHandler](handlers/standard#setResponseHandler),  [setJsonResponseHandler](handlers/standard#setJsonResponseHandler)) or through [coding](handlers/custom#response).

&nbsp;

&nbsp;