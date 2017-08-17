# Welcome to Configurapi

Configurapi is a configurable, event-driven API framework for rapid API developments.

## Install

`npm install -g configurapi-handler-http configurapi-runner-self`

## Configure

Config.yaml

    import:
      - configurapi-handler-http
    api:
      events:
        - name: get_v1_hello
          policies:
          - setResponseHandler:
            - 200
            - Hi $(hello)!

## Run

`./configurapi-runner-self`

### Test

```
$ curl http://localhost:8080/v1/hello/world

Hi world!

```

## Learn more

Want to learn more?  Check out [Getting Started](start.md)

