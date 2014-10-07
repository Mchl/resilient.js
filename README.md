# resilient.js [![Build Status](https://api.travis-ci.org/resilient-http/resilient.js.svg?branch=master)][travis] [![Stories in Ready](https://badge.waffle.io/resilient-http/resilient.js.png?label=ready&title=Ready)](https://waffle.io/resilient-http/resilient.js) [![Code Climate](https://codeclimate.com/github/resilient-http/resilient.js/badges/gpa.svg)](https://codeclimate.com/github/resilient-http/resilient.js) [![Gitter chat](https://badges.gitter.im/resilient-http/resilient.js.png)](https://gitter.im/resilient-http/resilient.js)

<img align="right" height="150" src="https://raw.githubusercontent.com/resilient-http/resilient-http.github.io/master/images/logo.png" />

A browser and [node.js](http://nodejs.org) fault tolerant, balanced, configurable and full featured HTTP client for distributed and reactive systems

For more information about the **resilient** and how it works, see the [project site](http://resilient-http.github.io)

**Note**: resilient is still a beta preview version

## Features

- Fault tolerant, transparent server fallback
- Client-side based balancer using a simple best availability algorithm
- Smart balancer logic based on server stats (latency, errors, requests...)
- Configurable balancer policy by weight
- Highly configurable (timeout, retry times, cache, wait delay fallback...)
- Server-side dynamic client configuration support (experimental)
- Built-in support for servers caching to improve reliability
- Parallel servers discovering for a faster
- Support for round robin scheduling algorithm (experimental)
- Cross engine (node.js and browsers ES5 compliant)
- Servers discovering based on the resilient high-level protocol
- Full HTTP features support (it uses [request](https://github.com/mikeal/request) and [lil-http](https://github.com/lil-js/http))
- Lightweight library (just 8KB gzipped)

<!--
## Introduction

Organisations working in disparate domains are independently discovering patterns
for building software that look the same.
These systems are more robust, more resilient, more flexible and better positioned to meet modern demands.

The system stays responsive in the face of failure.
This applies not only to highly-available, mission critical systems — any system that is not resilient will be unresponsive after a failure

### What resilient means?

Acording to the [reactive manifiesto](http://reactivemanifiesto.org), a good description for could be:

Resilience is achieved by replication, containment, isolation and delegation.
Failures are contained within each component, isolating components from each other
and thereby ensuring that parts of the system can fail and recover without
compromising the system as a whole. Recovery of each component is delegated to another (external)
component and high-availability is ensured by replication where necessary.
The client of a component is not burdened with handling its failures

## A client-side balancer?

Yes. `resilient` aims to delegate a part of the balance logic responsabilities on the client-side,

Web applications evolved notably in the latest years, achieving and delegating new responsabilities in the client side.
The Web (and therefore HTTP) is based on a client-server architecture

### How does it works?

An algorithm worth more than words

The following diagram represents from high level point of view the complete HTTP request flow and logic encapsulated in `resilient`
-->

## Installation

#### Node.js

```bash
npm install resilient
```

#### Browser

Via [Bower](http://bower.io)
```bash
bower install resilient
```

Via [Component](http://component.io/)
```bash
component install resilient-http/resilient.js
```

Or loading the script remotely
```html
<script src="//cdn.rawgit.com/resilient-http/resilient.js/0.1.0-beta.4/resilient.js"></script>
```

## Environments

It runs properly in any [ES5 compliant](http://kangax.github.io/compat-table/es5/) engine

- Node.js >= 0.6
- Chrome >= 5
- Firefox >= 3
- Safari >= 5
- Opera >= 10
- IE >= 9

## How it works?

An algorithm diagram worth more than words

<img src="http://rawgit.com/resilient-http/resilient-http.github.io/master/images/algorithm.svg" />

## Basic usage

If `require` is available, you must use it to fetch the module.
Otherwise it will be available as global exposed as `resilient`

```js
var Resilient = require('resilient')
```

#### Static servers

Define your server pool
```js
var servers = [
  'http://api1.server.com',
  'http://api2.server.com',
  'http://api3.server.com'
]
```

Create a new client and set the servers
```js
var client = Resilient({ service: { basePath: '/api/1.0' }})
client.setServers(servers)
```

Perform the request (the best available server will be use)
```js
client.get('/users', function (err, res) {
  // ...
})
```

#### Dynamic servers discovering

Define your discovering servers pool
```js
var servers = [
  'http://discover1.server.com',
  'http://discover2.server.com',
  'http://discover3.server.com'
]
```

Create a new client and set the discovering servers
```js
var client = Resilient({ service: { basePath: '/api/1.0' }})
client.discoveryServers(servers)
```

Perform the request (the best available server will be use)
```js
client.get('/users', function (err, res) {
  // ...
})
```

## API

### resilient([ options ])

Creates a new `resilient` client with a custom config

### Options

The options `object` has three different blocks and levels of configuration

##### Service

There are specific config options for the servers of the client service.
Resilient is a resource-oriented HTTP client, which could be ideal for RESTful Web services

- **servers** `array` - A list of valid URIs of servers to reach for the given service. Default `null`. It's recommended you use discovery servers instead
- **refresh** `number` - Servers list expiration time to live in miliseconds. Default `60` seconds. Once the time expired, will try to discovery it from discovery servers
- **retry** `number` - Number of times to retry if all requests failed.  Default `0`
- **retryWait** `number` - Number of milisenconds to wait before retry. Default to `1000`
- **updateOnRetry** `boolean` - Force to update service servers from discovery servers on each retry attempt. Recommended. You must define the discovery servers to use this feature. Default `true`

Specific shared configuration options for the HTTP client for final service requests

- **path** `string` - Server request path as part of the final URL
- **basePath** `string` - Server resource base path to share between all requests
- **method** `string` - Request HTTP method. Default to `GET`
- **data** `mixed` - Payload data to send as body request
- **headers** `object` - Map of strings representing HTTP headers to send to the server
- **timeout** `number` - Request maximum timeout in miliseconds before to abort it. Default to 10 seconds
- **auth** `object` - Authentication credentials to the server. Object must have the `user` and `password` properties

Browser specific options

- **async** `boolean` - Set to `false` if the request must be performed as synchronous operation (not recommended, browser only)
- **withCredentials** `boolean` - Whether to set the withCredentials flag on the XHR object. See [MDN][withcredentials] for more information
- **responseType** `string` - Define how to handle the response data. Allowed values are: `text`, `arraybuffer`, `blob` or `document`

Node.js specific options

See all HTTP options supported for `node.js` [here](https://github.com/mikeal/request#requestoptions-callback)

##### Balancer

- **enable** `boolean` - Enable/disable the smart client balancer. Default `true`
- **weight** `object` - Balacer point percentage weight for server scoring policy:
  - **success** `number` - Percentage weight for success request. Default to `15`
  - **error** `number` - Percentage weight for failed request. Default to `50`
  - **latency** `number` - Percentage weight for request average latency. Default to `35`

<!--
- **middleware** `function` - Balancer map middleware result (to do)
- **policy** `string` - Balancer priority policy. Supported values are: `latency` or `errors`. Detault `errors`
-->

##### Discovery

Specific configuration for discovery servers requests, behavior and logic

- **servers** `array` - A list of valid URIs of endpoints to use as discovery servers
- **cache** `boolean` - Enable/disable discovery servers cache in case of global fallback. Default `true`
- **cacheExpiration** `number` - Maximum cache time to live. Default to `10` minutes
- **retry** `number` - Number of times to retry if all requests failed.  Default `0`
- **retryWait** `number` - Number of milisenconds to wait before retry. Default to `1000`
- **parallel** `boolean` - Discover servers in parallel. Improve service availability and decrement delay times. Recommended. Default `true`

Specific shared configuration options for the HTTP client in discovering processes

- **path** `string` - Server request path as part of the final URL
- **basePath** `string` - Server resource base path to share between all requests
- **timeout** `number` - Server discovery network timeout in miliseconds. Default `2` seconds
- **auth** `object` - Authentication credentials required for the discovery server. Object must have the `user` and `password` properties
- **headers** `object` - Map of strings representing HTTP headers to send to the discovery server
- **method** `string` - Request HTTP method. Default to `GET`
- **data** `mixed` - Optional data to send as payload to discovery servers. Default `null`

### Request callback arguments

- **error** [Error|ResilientError](#error) - Response error, if happends. Otherwise `null`
- **response** [Object](#browser)|[http.IncomingMessage][httpMessage] - Response object

#### Response

##### Browser

- **data** `mixed` - Body response. If the MIME type is `JSON-compatible`, it will be transparently parsed
- **status** `number` - HTTP response status code
- **headers** `object` - Response headers
- **xhr** `object` - Original XHR instance
- **error** `mixed` - Error info, usually an `Error` instance (in case that an error happens)

##### Node.js

See [http.IncomingMessage][httpMessage]

#### Error

It will be an `Error` instance with the following members

- **message** `string` - Human readable error message
- **status** `number` - Internal error code or HTTP status
- **code** `number` - Optional error code (node.js only)
- **stack** `string` - Optional stack error trace
- **request** `object` - Original response object (node.js only). Optional
- **error** `Error` - Original throwed error object (node.js only). Optional
- **xhr** ``

##### Built-in error codes

- **1000** - All requests failed. No servers available
- **1001** - Cannot update discovery servers. Empty or invalid response body
- **1002** - Missing discovery servers. Cannot resolve the server
- **1003** - Cannot resolve servers. Missing data
- **1004** - Discovery server response is invalid or empty
- **1005** - Missing discovery servers during retry process
- **1006** - Internal state error (usually by an unexpected exception)

#### Events

Resilient client has a built-in support for internal states event dispacher and notifier to the public interface

This could be useful really useful while using an interceptor pattern in order to detect states and data changes.
You can intercept and change any request configuration and response subscribing to the pre/post hooks.
Note that mutation is required, you should modify it by reference and do not lose it

##### discovery.refresh
Arguments: `servers<Array>`, `resilient<Resilient>`

Fired every time that servers are updated from discovery servers

##### discovery.cache
Arguments: `servers<Array>`, `resilient<Resilient>`

Fired every time that servers cache is updated

##### request.start
Arguments: `options<Object>`, `resilient<Resilient>`

Fired as before a request is created

You can intercept and modify the request options on the fly,
but you must mutate it and do not lose its reference

##### request.finish
Arguments: `error<Error>`, `response<Object|http.IncomingMessage>`, `resilient<Resilient>`

Fired as after a request was completed

You can intercept and modify the error/response on the fly,
but you must mutate it and do not lose its reference

### resilient#send(path, options, callback)

Performs a custom request with the given options.
It's recommended using as generic interface to make multi verb requests

### resilient#get(path, options, callback)

Creates a GET request with optional custom options

### resilient#post(path, options, callback)

Creates a POST request with optional custom options

### resilient#put(path, options, callback)

Creates a PUT request with optional custom options

### resilient#del(path, options, callback)

Creates a DELETE request with optional custom options

### resilient#patch(path, options, callback)

Creates a PATCH request with optional custom options

### resilient#head(path, options, callback)

Creates a HEAD request with optional custom options

### resilient#setOptions(options)

Define custom options. See [supported options](#options)

### resilient#getOptions()
Return: `Options`

Retrieve the current options

### resilient#setServiceOptions(options)

Define custom [service-level options](#service)

### resilient#setDiscoveryOptions(options)

Define custom [service-level options](#discovery)

### resilient#getHttpOptions(type)
Return: `object`

Get a map of HTTP specific options

### resilient#getServers([ type = 'servicee' ])
Return: `Servers`

Return a the current servers list. Allowed types are: `service` and `discovery`

### resilient#discoveryServers([ servers ])
Return: `Servers`

Setter/Getter for discovery servers list

### resilient#updateServers([ callback ])

Force to update the servers list from discovery servers, if they are defined,
optionally passing a callback to handle the result

### resilient#setHttpClient(fn)

Use a custom HTTP client as proxy instead of the embedded `resilient` native HTTP client.

Useful to define use proxy for custom frameworks or libraries in your existent project when you need to deal with some complex HTTP pre/post hooks logic and exploit custom HTTP client features

If defined, all the outgoing requests through Resilient client will be proxied to it

Arguments passed to the client function:
- **options** `object` - Resilient HTTP [service options](#service)
- **callback** `function` - Request status handler. Expected arguments are: `error`, `response`

### resilient#restoreHttpClient()

Restore the native `resilient` HTTP client

### resilient#on(event, handler)

Subscribe to an event. See [supported events](#events)

### resilient#off(event, handler)

Unsubscribe a given event and its handler. See [supported events](#events)

### resilient#once(event, handler)

Subscribe to an event with a given handler just once time.
After fired, the handler will be removed

See [supported events](#events)

### resilient#flushCache()

Force to flush servers cache

### resilient#client()
Return: `Client`

Returns an HTTP client-only interface.
Useful to provide encapsulation from public usage and
avoid resilient-specific configuration methods

### resilient#areServersUpdated()
Return: `boolean`

Returns `true` if servers are up-to-date. Otherwise `false`

### resilient#balancer()
Return: `object`

Returns the current balancer config options

### resilient.VERSION
Type: `string`

Current semver library version

### resilient.CLIENT_VERSION
Type: `string`

Current semver HTTP client library version

### resilient.defaults
Type: `object`

Default config options

### resilient.Servers(list)

Create a new servers store

### resilient.Options(options)

Create a new options store

### resilient.Client(resilient)

Creates a new resilient HTTP client with public API

Useful to provide encapsulation to the resilient API and expose only the HTTP client (the common interface the developers want to consum)

### resilient.request(options [, cb])

Use the plain HTTP client

## Contributing

Wanna help? Cool! It will be appreciated :)

You must add new test cases for any new feature or refactor you do,
always following the same design/code patterns that already exist

### Development

Only [node.js](http://nodejs.org) is required for development

Clone the repository
```bash
$ git clone https://github.com/resilient-http/resilient.js.git && cd resilient.js
```

Install development dependencies
```bash
$ npm install
```

Install browser dependencies
```bash
$ bower install
```

Generate browser bundle source
```bash
$ make browser
```

Run tests (in a headless browser)
```bash
$ make test
```

Run tests (in real browsers)
```bash
$ make karma
```

## License

[MIT](http://opensource.org/licenses/MIT) © Tomas Aparicio and contributors

[travis]: http://travis-ci.org/resilient-http/resilient.js
[httpMessage]: http://nodejs.org/api/http.html#http_http_incomingmessage
