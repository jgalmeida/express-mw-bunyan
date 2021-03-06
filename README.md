# express-mw-bunyan

express middleware that implements bunyan as a logger to be used with `req.log`

<a href="https://nodei.co/npm/express-mw-bunyan/"><img src="https://nodei.co/npm/express-mw-bunyan.png?downloads=true"></a>

[![Build Status](https://img.shields.io/badge/build-passing-brightgreen.svg?style=flat-square)](https://travis-ci.org/joaquimserafim/express-mw-bunyan)![Code Coverage 100%](https://img.shields.io/badge/code%20coverage-100%25-green.svg?style=flat-square)[![ISC License](https://img.shields.io/badge/license-ISC-blue.svg?style=flat-square)](https://github.com/joaquimserafim/express-mw-bunyan/blob/master/LICENSE)[![NodeJS](https://img.shields.io/badge/node-6.1.x-brightgreen.svg?style=flat-square)](https://github.com/joaquimserafim/express-mw-bunyan/blob/master/package.json#L48)

[![JavaScript Style Guide](https://cdn.rawgit.com/feross/standard/master/badge.svg)](https://github.com/feross/standard)


### api
`const logger = require('express-mw-bunyan')`

```js
logger(
  bunyan createLogger object[,
  origin = default 'request'][,
  headerName = default 'X-Request-ID']
)
```

for more options to configure `bunyan.createLogger` check with [bunyan](https://github.com/trentm/node-bunyan#introduction)

**Notes:**
- should use a middleware like `express-mw-correlation-id` to generate the `req.id` if not `express-mw-bunyan` will generate the `req.id` itself by using `uuid.v4`
- if you want to set a different `headerName` but still use the default `origin` you should pass `undefined` in the `origin` place

### example

```js
const express = require('express')
const bunyan  = require('bunyan')

// instead of using `express-mw-correlation-id` can use the internal uuid
// {now}:{hostname}:{pid}:{conn creation(aprox.) base36}:{8 digit counter}
const setRequestId = require('express-mw-correlation-id')

// to capture the body/payload
const bodyParser = require('body-parser')



const logger = require('express-mw-bunyan')

// middlewares
app.use(bodyParser.json())
app.use(setRequestId())
app.use(logger(bunyan.createLogger({name: 'test'})), 'request_from_my_code')

// `req.log` will be exposed with all log methods from bunyan

app.get('/', (req, res) => {
  req.log.info('getting Hello World')
  res.send('Hello World')
})

app.post('/', (req, res) => {
  req.log.info('Posting something')
  res.status(201).send()
})

app.listen(3000)
```

example of the initial request
```js
{
  "name": "test",
  "hostname": "m-quim.local",
  "pid": 2695,
  "origin": "request_from_my_code",
  "id": "825efe65-a27d-4dca-936b-e74249095fb7",// REQUEST ID
  "level": 30,
  "req": {
    "method": "POST",
    "url": "/",
    "headers": {
      "host": "127.0.0.1:63135",
      "accept-encoding": "gzip, deflate",
      "user-agent": "node-superagent/2.3.0",
      "content-type": "application/json",
      "content-length": "7",
      "connection": "close"
    },
    "remoteAddress": "::ffff:127.0.0.1",
    "remotePort": 63136
  },
  "payload": {// USING A MODULE LIKE BODYPARSER TO PARSE THE BODY
    "a": 1
  },
  "msg": "start of the request",
  "time": "2016-12-10T13:22:56.639Z",
  "v": 0
}
```

example of the end reponse
```js
{
  "name": "test",
  "hostname": "m-quim.local",
  "pid": 2695,
  "origin": "request_from_my_code",
  "id": "825efe65-a27d-4dca-936b-e74249095fb7",// REQUEST ID
  "level": 30,
  "res": {
    "statusCode": 201,
    "header": "HTTP/1.1 201 Created\r\nX-Powered-By: Express\r\nX-Request-ID: 1481376176639:127.0.0.1:2695:iwj94lbz:10000002\r\nDate: Sat, 10 Dec 2016 13:22:56 GMT\r\nConnection: close\r\nContent-Length: 0\r\n\r\n"
  },
  "duration": 1.337593,// DURATION APROX. OF THE OPERATION IN MS
  "msg": "end of the response",
  "time": "2016-12-10T13:22:56.640Z",
  "v": 0
}
```



### ISC License (ISC)
