Ascoltatori
===========

[![Build
Status](https://travis-ci.org/mcollina/ascoltatori.png)](https://travis-ci.org/mcollina/ascoltatori)

__Ascoltatori__ is the publish/subscribe library that support every
broker/protocol out there.
This list currently includes:

* [RabbitMQ](http://www.rabbitmq.com/) and all implementations of
  the [AMQP](http://www.amqp.org/) protocol.
* [Redis](http://redis.io/)
* [Mosquitto](http://mosquitto.org/) and all implementations of the
  [MQTT](http://mqtt.org/) protocol.
* [ZeroMQ](http://www.zeromq.org/) without a central broker, so
  Ascoltatori can also be used in a P2P fashion.

> Ascoltatori is an italian word which means listeners.
An Ascoltatore is therefore a single listener.

## Usage

__Ascoltatori__ is built to be extremely easy to use, and can provide a
useful abstraction for every compatible pub/sub broker.
In this way you can choose whatever broker suits you.

```
var ascoltatori = require('ascoltatori');

var ascoltatore = new ascoltatori.MemoryAscoltatore();

ascoltatore.subscribe("hello/*", function() {
  // this will print { '0': "hello/42", '1': "a message" }
  console.log(arguments); 
  process.exit(0);
});

ascoltatore.publish("hello/42", "a message", function() {
  console.log("message published");
});
```

See the tests for more examples regarding RedisAscoltatore,
AMQPAscoltatore, ZeromqAscoltatore, MQTTAscoltatore.

In the test/common.js file you can find all the options for
all the ascoltatori.

All ascoltatori supports the use of a wildcards, so everything
should work smoothly on every broker.
You might find some differences, and in that case file a bug
report, so I can fix them.

### Domain support

__Ascoltatori__ properly supports the [node.js domain API](http://nodejs.org/api/domain.html).
To use it, you have to call the `registerDomain` function on your
_Ascoltatore_, and it will take care of routing the exceptions to the
given domain. Look at this example:
```
var ascoltatori = require('ascoltatori');
var domain      = require("domain");

var d = domain.create();
d.on("error", function() {
  console.log(arguments); 
  process.exit(0);
});

var ascoltatore = new ascoltatori.MemoryAscoltatore();
ascoltatore.registerDomain(d);

ascoltatore.subscribe("*", function() {
  throw new Error();
});

ascoltatore.publish("hello/42", "a message", function() {
  console.log("message published");
});
```


## Dependencies

This library does not depend directly on redis, AMQP (RabbitMQ),
zmq, MQTT.js, but rather it encourages you to pass them to the
ascoltatori via an options object, like so (for Redis):

```
var ascoltatori = require('ascoltatori');

var ascoltatore = new ascoltatori.RedisAscoltatore({
  redis: require('redis'),
  db: 12,
  port: 424242,
  host: 192.168.42.42
});

ascoltatore.subscribe("hello/*", function() {
  // this will print { '0': "hello/42", '1': "a message" }
  console.log(arguments); 
  process.exit(0);
});

ascoltatore.publish("hello/42", "a message", function() {
  console.log("message published");
});

```

If you feel one more option is missing, feel free to fork this library,
add it, and then send a pull request.

## Install

```
npm install ascoltatori
```

## Contributing to Ascoltatori

* Check out the latest master to make sure the feature hasn't been
  implemented or the bug hasn't been fixed yet
* Check out the issue tracker to make sure someone already hasn't
  requested it and/or contributed it
* Fork the project
* Start a feature/bugfix branch
* Commit and push until you are happy with your contribution
* Make sure to add tests for it. This is important so I don't break it
  in a future version unintentionally.
* Please try not to mess with the Makefile and package.json. If you
  want to have your own version, or is otherwise necessary, that is
  fine, but please isolate to its own commit so I can cherry-pick around
  it.

## LICENSE - "MIT License"

Copyright (c) 2012-2013 Matteo Collina, http://matteocollina.com

Permission is hereby granted, free of charge, to any person
obtaining a copy of this software and associated documentation
files (the "Software"), to deal in the Software without
restriction, including without limitation the rights to use,
copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the
Software is furnished to do so, subject to the following
conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES
OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT
HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY,
WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING
FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR
OTHER DEALINGS IN THE SOFTWARE.
