# Websocket

Websocket connections are made to the root uri of the API, e.g `wss://dev.api.fuelrats.com/`.

You may authenticate by providing an OAuth bearer token as a "bearer" parameter of the Websocket URI, e.g `wss://dev.api.fuelrats.com?bearer=7c14277bada1b00e1257ee73bc157c628fb8ea251e762d7c`.


The Fuel Rats API sends all traffic encoded in JSON and requires the protocol to be specified as `FR-JSONAPI-WS` on connection, connections not specifying this protocol will be rejected.

## Endpoint Requests

Websocket requests is made in a root JSON array in the following format:
```javascript
[
  state,
  endpoint,
  query,
  body
]
```

**Sending a JSON object that does not conform to this standard or sending any other unparsable data will result in the server closing the websocket connection with a client error signal.**

* **state**: The state parameter can be any random string, it is used as a unique identifier that will be sent back in replies allowing you to identify what request a response is in reply to.
* **endpoint**: An array pointing to a specific resource you would like to make a request to, this is basically the Websocket API equivalent to a URL, and the WebSocket endpoint equivalent to each endpoint is specified in that endpoint's documentation.
* **query**: Request query information object, corresponding to the url query parameters you would send in an HTTP request.
* **body**: The body data of the message, corresponding to the information you would have in the body of an HTTP request.


Responses are as follows:
```javascript
[
  state, // the state you sent in your request
  status, // HTTP status code
  body // Response body
]
```


Here is an example WebSocket request searching for a rescue:

```javascript
[
  "bRploBucQJO2Z3Pwtj0Y0P0ofMYiEjS5",
  ["rescues", "search"],
  {
    "sort": "-createdAt",
    "page": {
      "number": 3,
      "size": 50
    },
    "filter": {
      "platform": "pc"
    }
  },
  {}
]
```

And here is a response to this request:
```javascript
[
  "bRploBucQJO2Z3Pwtj0Y0P0ofMYiEjS5",
  200,
  {
    // JSONAPI result document here
  }
]
```

## Events
Events are a way for the Fuel Rats API as well as third party applications to share live event updates to listeners over WebSocket.

Events are received in the following format:
```javascript
[
  event,
  sender,
  resourceId,
  data
]
```

* **event**: Identifier for the specific event, consisting of a namespace and an event name. The API broadcasts events under the "fuelrats" namespace, to broadcast your own events, you must request that a namespace be assigned to your registered OAuth client.
* **sender**: Sender of the event (User ID), for third party events this will be the user that sent the broadcast, for API events this will be the user that made the change causing the event (e.g updated a rescue.)
* **resourceId** : The ID of the resource, see `data` for more information.
* **data**: This data will be an resource which content differs depending on the content in question, especially for third party broadcasted events as this is entirely up to the broadcaster.

**All authenticated websocket connections receive the default API rescue events. (fuelrats.rescuecreate, fuelrats.rescueupdate, fuelrats.rescuedelete).**

Here is a sample `fuelrats.rescuecreate` event:

```javascript
[
  "fuelrats.rescuecreate", // Event name
  "793be7be-c6b5-4160-b7aa-2c6663ffe382", // Sender ID
  "2963ed46-ef5a-434e-a402-eed165ddd689", // Resource ID
  {
    "jsonapi": {
      "version": "1.0",
      "meta": {
        "apiVersion": "3.0.0"
      }
    },
    "meta": {
      "apiVersion": "3.0.0"
    },
    "links": {
      "self": "https://dev.api.fuelrats.com/rescues/2963ed46-ef5a-434e-a402-eed165ddd689"
    },
    "data": {
      "type": "rescues",
      "id": "2963ed46-ef5a-434e-a402-eed165ddd689",
      "attributes": {
        "client": "some_client",
        "clientNick": "some_client",
        "clientLanguage": "en-US",
        "commandIdentifier": 1,
        "codeRed": false,
        "data": {},
        "notes": "",
        "platform": null,
        "system": null,
        "title": null,
        "unidentifiedRats": [],
        "createdAt": "2020-07-08T20:20:27.721Z",
        "updatedAt": "2020-07-08T20:20:27.721Z",
        "status": "open",
        "outcome": null,
        "quotes": []
      },
      "meta": {},
      "relationships": {
        "rats": {
          "links": {
            "self": "https://dev.api.fuelrats.com/rescues/2963ed46-ef5a-434e-a402-eed165ddd689/relationships/rats",
            "related": "https://dev.api.fuelrats.com/rescues/2963ed46-ef5a-434e-a402-eed165ddd689/rats"
          },
          "data": []
        },
        "firstLimpet": {
          "links": {
            "self": "https://dev.api.fuelrats.com/rescues/2963ed46-ef5a-434e-a402-eed165ddd689/relationships/firstLimpet",
            "related": "https://dev.api.fuelrats.com/rescues/2963ed46-ef5a-434e-a402-eed165ddd689/firstLimpet"
          },
          "data": null
        },
        "epics": {
          "links": {
            "self": "https://dev.api.fuelrats.com/rescues/2963ed46-ef5a-434e-a402-eed165ddd689/relationships/epics",
            "related": "https://dev.api.fuelrats.com/rescues/2963ed46-ef5a-434e-a402-eed165ddd689/epics"
          },
          "data": []
        }
      },
      "links": {
        "self": "https://dev.api.fuelrats.com/rescues/2963ed46-ef5a-434e-a402-eed165ddd689"
      }
    },
    "included": []
  }
]
```

### Subscribing to an event
Subscribing to an event is done by a request as follows:
```javascript
[
  "bRploBucQJO2Z3Pwtj0Y0P0ofMYiEjS5", // state
  ["events", "subscribe"], // endpoint
  {
    "events": ["rattracker.fr", "rattracker.wr"] // events you wish to subscribe to
  }
]
```

### Unsubscribing from an event
Unsubscribing is performed very similarily:
```javascript
[
  "bRploBucQJO2Z3Pwtj0Y0P0ofMYiEjS5", // state
  ["events", "unsubscribe"], // endpoint
  {
    "events": ["rattracker.fr", "rattracker.wr"] // events you wish to unsubscribe from
  }
]
```

### Broadcasting an event
Broadcasts can be performed via WebSocket OR HTTP (see POST /events/:event)

Here is an example of broadcasting via WebSocket:

```javascript
[
  "bRploBucQJO2Z3Pwtj0Y0P0ofMYiEjS5", // state
  ["events", "broadcast"], // endpoint
  {
    "event": "rattracker.fr" // event you wish to broadcast
  },
  {
    // Data you wish to include in your broadcast
  }
]
```

**Do not use this feature to transfer large amounts of data, broadcasting a message larger than 512KiB will result in the request being rejected, your websocket connection terminated, and may result in your client being banned.**

Your applications can not broadcast on fuelrats.* namespaces, this is reserved for the API, if you wish to make WebSocket broadcasts you must contact the tech team so they can grant your application a namespace to broadcast on.
