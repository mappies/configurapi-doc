# Getting Started

## Overview

Configurapi is a configuration-driven, event-based API framework that allows developers to deliver API quicky.  In this short tutorial, we will create a simple ping API that echos back some data.

## Create a project

First we create a directory for our project.

    mkdir ping-api
    cd ping-api

## Installation

A configurapi application needs to a runner to run them.  There are many runners available.  You can see its full list on the [Runner](runner) page. In this example, we will create a self hosted node.js application, so we will use configurapi-runner-self package. 

    npm install -g configurapi-runner-self

We also want the API to be a JSON API, so we need to install configurapi-handler-json package as well.

    npm install configurapi-handler-json

## Config file

Since configurapi is a configuration-driven framework, we need a configuration file.  Let's create one now.

    touch config.yaml

## Your first API route

Our simple ping API will have one POST route.  The route will recieve a json request and simply return a payload back. We can do all of that by updating the config.yaml as follows.

    import:
      - configurapi-handler-json
    api:
      events:
        - name: post_v1_ping
          policies:
          - setJsonResponseHandler:
            - 200
            - {pong: true}

The config file has two sections -- *import* and *api*.  The *import* section lists all packages we plan to use for the API.  It will import all event handlers defined in each package and make it available to use in the *api* section.

The *api* section defines events or routes the software is expected to handle.  In this example, we expose one route -- post_v1_ping -- which is basically an event name of POST /v1/ping.  You can learn more about URL to Event mapping on [Event](event) page.

The *policies* describes a chain of handlers we want to process all incoming requests to this route.  In this example, we want to return a JSON response { pong: true } back.

## Run it

Let's run our first API route by running the following command on your shell.  

    configurapi-runner-self -p 8080

This will run our API on port 8080.  Let's call it.

    curl http://localhost:8080/v1/ping -X POST

The API should response with

    {"pong":true}

Congratulation, you have created an API without writing any codes!

If you encounter a problem, make sure that config.yaml is on your current working directory.

## Get data from request payloads

So far, our API is too simple as it always returns a static response.  In the real world, we often need to extract data from a request payload and manipulate it.  Let's change our API to return something more interesting by chaning the config to the following:

    import:
      - configurapi-handler-json
    api:
      events:
        - name: post_v1_ping
          policies:
          - setJsonResponseHandler:
            - 200
            - {pong: Hi $($event.payload.from)}

With the new config, we will extract the payload and retrieve a value of property *from* and return it as a response.

    curl http://localhost:8080/v1/ping -X POST  -H "Content-Type: application/json" -X POST -d '{"from": "some one"}'

We should see the following response. 

    {"pong":"Hi some one"}

## What's next?

Congratulation!  You have completed a quick tutorial on how to use configurapi which should give you enough to understand the basic principal of a configuration driven API framework.

Since configurapi is also an event-driven framework, we will expore [Event](event) in more details next.