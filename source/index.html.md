---
title: Bluzelle API Reference

language_tabs: # must be one of https://git.io/vQNgJ

toc_footers:

includes:


search: true
---

# Introduction

Bluzelle combines the sharing economy with the token economy - renting individuals' computer storage space to earn tokens while dApp developers use tokens to have their dApp's data stored and managed.


# Getting Bluzelle

> Our client JavaScript library can be installed through `npm`.

```shell
npm install bluzelle
```

> You import *Bluzelle* into your application the same as any other library.

```javascript
const bluzelle = require('bluzelle');
```

Our Lovelace release will provide developer tools for *JavaScript*, *NEO*, and *Soliditity*, as well as a generic *WebSocket* interface for communicating with the swarm.

This documentation covers our **JavaScript** and **WebSocket** tools only. Each function in the JavaScript API wraps a request-response pair in the WebSocket API.


## CRUD Application

Click [here](CRUDWeb) to access our online *Bluzelle* CRUD application, developed using the JavaScript library.




# JS API



## connect(ws, uuid)

> Our JavaScript library makes extensive use of <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise">JavaScript promises</a> to wrap the underlying request-response architecture.

> You may also use <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function">async/await</a> syntax to avoid the use of `then`.


```javascript

// promise syntax
bluzelle.connect('ws://1.1.1.1:8000', UUID)
  .then(() => console.log('Connection established!'),
        () => console.log('Connection refused!'));

// async/await syntax
await bluzelle.connect('ws://1.1.1.1:8000', UUID);
```


*Bluzelle* uses `UUID`'s to identify distinct databases on a single swarm. We recommend using <a href="https://en.wikipedia.org/wiki/Universally_unique_identifier#Version_4_(random)">Version 4 of the universally unique identifier</a>. To connect, pass in the WebSocket address of the entry point and our system will automatically communicate and connect with the distributed swarm.


---------

Argument  | Description
----------|------------
ws       | The WebSocket entry point to connect to
uuid     | The universally unique identifier (UUID), <a href="https://en.wikipedia.org/wiki/Universally_unique_identifier#Version_4_(random)">Version 4 is recommended</a>


### Returns

Nothing.


### Fail Conditions

Fails when a connection could not be established.

<aside class="warning">
You must replace <code>UUID</code> with your personal UUID.
</aside>

<aside class="notice">
This function is an abstraction over establishing a WebSocket connection, and thus does not wrap a particular command in the WebSocket API.
</aside>


## has(key)

```javascript
// promise syntax
bluzelle.has('mykey').then(hasMyKey => { ... }, error => { ... });

// async/await syntax
const hasMyKey = await bluzelle.has('mykey');
```


Query to see if a key is in the database.

----------

Argument  | Description
----------|------------
key       | The name of the key to query


### Returns

A boolean value, `true` of `false`, representing whether or not the key is in the database.


### Fail Conditions

Fails when a response is not received from the connection.



## keys()

```javascript
// promise syntax
bluzelle.keys().then(keys => { ... }, error => { ... });

// async/await syntax
const keys = await bluzelle.keys();
```


Retrieve a list of all keys.

----------

Argument  | Description
----------|------------


### Returns

An array of strings representing keys in the database. ex. `["Key1", "Key2", ...]`


### Fail Conditions

Fails when a response is not received from the connection.


## create(key)


```javascript
// promise syntax
bluzelle.create('mykey', { a: 13 }).then(() => { ... }, error => { ... });

// async/await syntax
await bluzelle.create('mykey', { a: 13 });
```


Creates a field. *Bluzelle* supports JavaScript types that are `JSON`-serializable (strings, objects, numbers, booleans, arrays, and combinations theereof) and also serial data in the form of an <a href="">ArrayBuffer</a>. Functions are not valid.


----------

Argument  | Description
----------|------------
key       | The name of the key
value     | The value to set the key


### Returns

Nothing.


### Fail Conditions

Fails when a response is not received from the connection or invalid value.



## read(key)

```javascript
// promise syntax
bluzelle.read('mykey').then(value => { ... }, error => { ... });

// async/await syntax
const value = await bluzelle.read('mykey');
```


Retrieve the value of a key.

----------

Argument  | Description
----------|------------
key      |  The key to retrieve.


### Returns

The value of the key. This is anything that is valid in the *Bluzelle* database. (See <a href="#update-key-value">`update(key, value)`</a>)


### Fail Conditions

Fails when a response is not recieved or the key does not exist in the database.


## update(key, value)

```javascript
// promise syntax
bluzelle.update('mykey', { a: 13 }).then(() => { ... }, error => { ... });

// async/await syntax
await bluzelle.update('mykey', { a: 13 });
```


> Serial data example.

```javascript

const arr = new Uint8Array(1000);

arr.fill(4, 200, 400); // Fill 4 in slots 200-400.

await bluzelle.update('mykey', arr.buffer);
```


Update a field in the database.

----------

Argument  | Description
----------|------------
key       | The name of the key to query
value     | The value to set the key


### Returns

Nothing.


### Fail Conditions

Fails when a response is not received from the connection or invalid value.



## remove(key)


```javascript
// promise syntax
bluzelle.remove('mykey').then(() => { ... }, error => { ... });

// async/await syntax
await bluzelle.remove('mykey');
```


Deletes a field from the database.

----------

Argument  | Description
----------|------------
key       | The name of the key to delete.


### Returns

Nothing.


### Fail Conditions

Fails when a response is not received from the connection or key not in database.


## ping()


```javascript
// promise syntax
bluzelle.ping().then(() => { ... }, error => { ... });

// async/await syntax
await bluzelle.ping();
```


Pings the current connection.

----------

Argument  | Description
----------|-----------


### Returns

Nothing.


### Fail Conditions

Fails when no response is received.



# WebSocket API

The *Bluzelle* architecture consists of a request-response system through WebSockets.

-------

Each WebSocket **request** is a JSON object and may have the following fields:

- `cmd`: The identifier of the request. (ex. `"read"`, `"update"`, ...)
- `data`: Complementary data for the request. 
- `bzn-api`: Used internally by the daemon. Set to constant `"crud"` for this guide.
- `db-uuid`: The `UUID` for your database.
- `request-id`: (Optional) An identifier for this request. The value is mirrored in the `response-to` field of the request.

-------

Likewise, each WebSocket **response** from a *Bluzelle* daemon is a JSON object and may have the following fields:

- `data`: Complementary data for the response.
- `response-to`: The `request-id` field of the initial request, if present.
- `error`: If present, this is an error string signifying that the request has failed.


<aside class="notice">
The <em>Bluzelle</em> database reads and writes values encoded in strings. It is the responsibility of the programmer to interpret these values to meet their desired functionality.
</aside>


## read

```json-doc

// Request

{
  "cmd": "read",
  "data": {
    "key": "mykey"
  },
  "bzn-api": "crud",
  "db-uuid": "4982e0b0-0b2f-4c3a-b39f-26878e2ac814",
  "request-id": 13
}


// Response

{
  "data": {
    "value": "He932NLA"
  },
  "response-to": 13
}

// or

{
  "error": "Key not in database",
  "response-to": 13
}

```


Reads data from a given key. Data is in the form of a <code>base64</code>-encoded string.


## update

```json-doc

// Request

{
  "cmd": "update",
  "data": {
    "key": "mykey",
    "value": "GNJjA39s"
  },
  "bzn-api": "crud",
  "db-uuid": "4982e0b0-0b2f-4c3a-b39f-26878e2ac814",
  "request-id": 45
}


// Response

{
  "response-to": 45
}

// or

{
  "error": "Invalid value",
  "response-to": 45
}

```

Updates data to a given key.



## delete

```json-doc

// Request

{
  "cmd": "delete",
  "data": {
    "key": "mykey",
  },
  "bzn-api": "crud",
  "db-uuid": "4982e0b0-0b2f-4c3a-b39f-26878e2ac814",
  "request-id": 45
}


// Response

{
  "response-to": 45
}

// or

{
  "error": "Key not in database",
  "response-to": 45
}

```

Deletes a given key.




## has

```json-doc

// Request

{
  "cmd": "has",
  "data": {
    "key": "mykey",
  },
  "bzn-api": "crud",
  "db-uuid": "4982e0b0-0b2f-4c3a-b39f-26878e2ac814",
  "request-id": 99
}


// Response

{
  "data": {
    "value": true // false
  },
  "response-to": 99
}

```

Query if a key exists in the database.


## keys

```json-doc


// Request

{
  "cmd": "keys",
  "bzn-api": "crud",
  "db-uuid": "4982e0b0-0b2f-4c3a-b39f-26878e2ac814",
  "request-id": 45
}


// Response

{
  "data": {
    "value": ["key1", "key2", "hello", "world", ...]
  },
  "response-to": 45
}

```

Obtain a list of keys in the database.


## ping

```json-doc
// Request

{
  "cmd": "ping",
  "bzn-api": "ping",
  "request-id": 5
}


// Response

{
  "response-to": 5
}
```

Used to verify a connection.



### Redirection

```json
{
    "data" : {
        "leader-id" : "137a8403-52ec-43b7-8083-91391d4c5e67",
         "leader-url":"1227.0.0.1",
         "leader-port": 49153
    },
    "error" : "NOT_THE_LEADER",
    "request-id" : 0
}
```


Some requests require you to be talking to the swarm leader. In this case, you will get a response of the this nature, and should reconnect at the address and port given.
