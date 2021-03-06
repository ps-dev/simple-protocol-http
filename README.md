# Simple Protocol Http

This module normalizes http responses using Simple Protocol.  HTTP response bodies are parsed as JSON by default but gracefully fall back to text if the response body is not valid JSON.

## What is simple protocol?

Simple protocol is *simple*:  
1) Never intentionally throw exceptions / always return with a 200 status code.  
2) Return a valid JSON object like this for a success:
```
{
  success: true,
  payload: {
    // the result of the operation, i.e. an http response body
  }
}
```
3) Return a valid JSON object like this for an error:
```
{
  success: false,
  error: {
    // error details or object
  }
}
```
That's it!  Both success and error cases are handled the same way and can follow the same code path.

## Installation
```
npm i --save simple-protocol-http
```

## API

```
const { get, post, put, remove} = require('simple-protocol-http')

let result = await get(url)
let result = await post(url, payload)
let result = await put(url, payload)
let result = await remove(url)

```

If you want to send custom fetch options (i.e. send custom headers, etc):

```
const { get, post, put, remove} = require('simple-protocol-http').options

let result = await get(fetchOptions, url)
let result = await post(fetchOptions, url, payload)
let result = await put(fetchOptions, url, payload)
let result = await remove(fetchOptions, url)

```

The whole enchilada, i.e. full referential transparency:
```
const { get, post, put, remove} = require('simple-protocol-http').full

let result = await get(fetch, fetchOptions, url)
let result = await post(fetch, fetchOptions, url, payload)
let result = await put(fetch, fetchOptions, url, payload)
let result = await remove(fetch, fetchOptions, url)

```

## Examples Making Requests to Restful Endpoints

### For successful / 200-range responses
```
const { get } = require('simple-protocol-http')
let result = await get('http://www.example.com/api')
```
If the server returns this with a 200 status code:
```
{
  value: 'foo'
}
```
The value of result is:
```
{
  success: true,
  payload: {
    value: 'foo'
  },
  meta: {
    status: 200,
    statusText: 'OK',
    headers: {...}
  }
}
```

### For error / non-200-range responses:
```
const { post } = require('simple-protocol-http')
let result = await post('http://www.example.com/api', {...})
```
If the server returns this with a 400 status code:
```
{
  type: 'ValidationError',
  message: 'Some field is invalid'
}
```
The value of result is:
```
{
  success: false,
  error: {
    type: 'ValidationError',
    message: 'Some field is invalid'
  },
  meta: {
    status: 400,
    statusText: 'Bad Request',
    headers: {...}
  }
}
```

## Example Using Simple Protocol Endpoints

### For Success Responses:
```
const { post } = require('simple-protocol-http')
let result = await post('http://www.example.com/api', {...})
```
If the server returns this:
```
{
  success: true,
  payload: {
    foo: 'bar'
  }
}
```
This module will return this:
```
{
  success: true,
  payload: {
    foo: 'bar'
  },
  meta: {
    status: 200,
    statusText: 'OK',
    headers: {...}
  }
}
```

### For Unsuccessful Responses:
```
const { post } = require('simple-protocol-http')
let result = await post('http://www.example.com/api', {...})
```
If the server returns this:
```
{
  success: false,
  error: {
    message: 'You posted something invalid'
  }
}
```
This module will return this:
```
{
  success: false,
  error: {
    message: 'You posted something invalid'
  },
  meta: {
    status: 200,
    statusText: 'OK',
    headers: {...}
  }
}
```
