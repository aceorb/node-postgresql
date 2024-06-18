#node-postgres

[![Build Status](https://secure.travis-ci.org/brianc/node-postgres.svg?branch=master)](http://travis-ci.org/brianc/node-postgres)
[![Dependency Status](https://david-dm.org/brianc/node-postgres.svg)](https://david-dm.org/brianc/node-postgres)

PostgreSQL client for node.js.  Pure JavaScript and optional native libpq bindings.

## Install

```sh
$ npm install pg
```


## Examples

### Client pooling

Generally you will access the PostgreSQL server through a pool of clients.  A client takes a non-trivial amount of time to establish a new connection. A client also consumes a non-trivial amount of resources on the PostgreSQL server - not something you want to do on every http request. Good news: node-postgres ships with built in client pooling.

```javascript
var Pool = require('pg').Pool;

var config = {
  user: 'foo',
  password: 'secret',
  database: 'my_db',
  port: 5432
};

var pool = new Pool(config);

//this initializes a connection pool
//it will keep idle connections open for a (configurable) 30 seconds
//and set a limit of 10 (also configurable)
pool.connect(function(err, client, done) {
  if(err) {
    return console.error('error fetching client from pool', err);
  }
  client.query('SELECT $1::int AS number', ['1'], function(err, result) {
    //call `done()` to release the client back to the pool
    done();

    if(err) {
      return console.error('error running query', err);
    }
    console.log(result.rows[0].number);
    //output: 1
  });
});
```

node-postgres uses [pg-pool](https://github.com/brianc/node-pg-pool.git) to manage pooling and only provides a very thin layer on top.  

It's _highly recommend_ you read the documentation for [pg-pool](https://github.com/brianc/node-pg-pool.git)


[Here is a tl;dr get up & running quickly exampe](https://github.com/brianc/node-postgres/wiki/Example)

### Client instance

Sometimes you may not want to use a pool of connections.  You can easily connect a single client to a postgres instance, run some queries, and disconnect.

```javascript
var pg = require('pg');

var conString = "postgres://username:password@localhost/database";

var client = new pg.Client(conString);
client.connect(function(err) {
  if(err) {
    return console.error('could not connect to postgres', err);
  }
  client.query('SELECT NOW() AS "theTime"', function(err, result) {
    if(err) {
      return console.error('error running query', err);
    }
    console.log(result.rows[0].theTime);
    //output: Tue Jan 15 2013 19:12:47 GMT-600 (CST)
    client.end();
  });
});

```

## [More Documentation](https://github.com/brianc/node-postgres/wiki)

## Native Bindings

To install the [native bindings](https://github.com/brianc/node-pg-native.git):

```sh
$ npm install pg pg-native
```


node-postgres contains a pure JavaScript protocol implementation which is quite fast, but you can optionally use native bindings for a 20-30% increase in parsing speed. Both versions are adequate for production workloads.

To use the native bindings, first install [pg-native](https://github.com/brianc/node-pg-native.git).  Once pg-native is installed, simply replace `require('pg')` with `require('pg').native`.

node-postgres abstracts over the pg-native module to provide exactly the same interface as the pure JavaScript version. Care has been taken to keep the number of api differences between the two modules to a minimum; however, it is recommend you use either the pure JavaScript or native bindings in both development and production and don't mix & match them in the same process - it can get confusing!

## Features

* pure JavaScript client and native libpq bindings share _the same api_
* optional connection pooling
* extensible js<->postgresql data-type coercion
* supported PostgreSQL features
  * parameterized queries
  * named statements with query plan caching
  * async notifications with `LISTEN/NOTIFY`
  * bulk import & export with `COPY TO/COPY FROM`

## Contributing

__We love contributions!__

If you need help getting the tests running locally or have any questions about the code when working on a patch please feel free to email me or gchat me.

I will __happily__ accept your pull request if it:
- __has tests__
- looks reasonable
- does not break backwards compatibility

Information about the testing processes is in the [wiki](https://github.com/brianc/node-postgres/wiki/Testing).

Open source belongs to all of us, and we're all invited to participate!

## Support

If at all possible when you open an issue please provide
- version of node
- version of postgres
- smallest possible snippet of code to reproduce the problem

Usually I'll pop the code into the repo as a test.  Hopefully the test fails.  Then I make the test pass.  Then everyone's happy!

If you need help or run into _any_ issues getting node-postgres to work on your system please report a bug or contact me directly.  I am usually available via google-talk at my github account public email address.

I usually tweet about any important status updates or changes to node-postgres on twitter.
Follow me [@briancarlson](https://twitter.com/briancarlson) to keep up to date.


## Extras

node-postgres is by design pretty light on abstractions.  These are some handy modules we've been using over the years to complete the picture.
Entire list can be found on [wiki](https://github.com/brianc/node-postgres/wiki/Extras)

## License

Copyright (c) 2010-2016 Brian Carlson (brian.m.carlson@gmail.com)

 Permission is hereby granted, free of charge, to any person obtaining a copy
 of this software and associated documentation files (the "Software"), to deal
 in the Software without restriction, including without limitation the rights
 to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
 copies of the Software, and to permit persons to whom the Software is
 furnished to do so, subject to the following conditions:

 The above copyright notice and this permission notice shall be included in
 all copies or substantial portions of the Software.

 THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
 IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
 FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
 AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
 LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
 OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
 THE SOFTWARE.
