---
title: Properly extending EventEmitter in node.js
id: 129
categories:
  - Hacking
date: 2013-10-08 13:21:48
tags:
---

A particularly powerful pattern in node.js is that of EventEmitters. One of the core architectural pieces of Donna's machinery, Qred, makes use of the EventEmitter pattern to allow other parts of the system to learn when a job, or a particular job, has been completed. 

JavaScript is certainly not known for its intuitive syntax and idioms when it comes to extending objects and setting up prototype chains. While there are some fairly well-defined best practices, it's easy to become careless and do the wrong thing. I found myself recently bitten when playing with making objects that emit events in node.js because I didn't pay close attention to how I was setting up my prototype chains.

To create a JavaScript object that is based on another object, you have to set up its prototype chain. This involves putting some kind of object into the `prototype` field of your object. So, the question then becomes: _what_ do we put in this field?

Well, you want your object's prototype to be an object that has a prototype which is the object you're trying to extend the functionality of. One of the ways we first learn to do something like this in JavaScript is with the `new` keyword. However, more modern versions of JavaScript have introduced newer ways to create objects based on a prototype. So we also have `Object.create()`. Furthermore, node.js provides a utility function in the `util` module called `inherits`. However, under the hood, it's based on `Object.create`, and also sets up some constructor and superclass information. 

So, what's the difference in setting your prototype using `new` vs. using `Object.create`? It's subtle, but important. When you use `new`, a new object based on the prototype of the specified object is created, _and_ the initialization code is run.  When you use `Object.create`, the new object is created with the prototype chain set up, but the initialization code is not run.

This is an important distinction in the case of setting up `EventEmitter` functionality in node.js. When you call `new EventEmitter` some internal state is initialized, but **only if it did not exist yet**. This means, if you initialize your custom `EventEmitter` prototype chain using `new`, **all subsequent instances** of your custom emitter will share some internal structures. One of those structures is a list of events to be emitted, contained in the `_events` field of the object. The code of interest in the **EventEmitter** initialization looks like this:

`this._events = this._events || {};`

If this initialization code is run, then any objects created further down the initialization chain will see the already-created `_events` object, and not create a new one. So now a single `_event` queue is shared amongst all of the objects. This means that if you create multiple instances of your custom `EventEmitter`, all of the instances with registered event listeners will fire these listener for **any event** emitted from **any instance**. This is most likely not the behavior you are looking for.

To exhibit the difference, let's look at a small code example. We'll create two custom **EventEmitter** objects, one that sets up its prototype chain using `new EventEmitter` and one that sets up its prototype chain using `Object.create(EventEmitter.prototype)` via the `util.inherits` helper function.

`<pre>
var EventEmitter = require('events').EventEmitter;
var util = require('util');

function GoodEmitter() {
    EventEmitter.call(this);
}
util.inherits(GoodEmitter, EventEmitter);

function BadEmitter() {
    EventEmitter.call(this);
}
BadEmitter.prototype = new EventEmitter();

var good1 = new GoodEmitter();
good1.on('ev', function(msg) { console.log("Good Instance 1: "+msg); });
var good2 = new GoodEmitter();
good2.on('ev', function(msg) { console.log("Good Instance 2: "+msg); });
good1.emit('ev', 'GoodEmitter: Emitting from Instance 1');

var bad1 = new BadEmitter();
bad1.on('ev', function(msg) { console.log("Bad Instance 1: "+msg); });
var bad2 = new BadEmitter();
bad2.on('ev', function(msg) { console.log("Bad Instance 2: "+msg); });
bad1.emit('ev', 'BadEmitter: Emitting from Instance 1');
</pre>`

If you run this code using node.js, you will see:
`
$ node ee.js
Good Instance 1: GoodEmitter: Emitting from Instance 1
Bad Instance 1: BadEmitter: Emitting from Instance 1
Bad Instance 2: BadEmitter: Emitting from Instance 1
`

So we see quite clearly: we have both instances of the bad emitter object responding to an event published by just one of the bad emitter instances. The same pattern with the good emitter behaves as expected, only firing listeners registered on the object that emitted the event.

There may be cases where one could argue for the behavior that I have called the "bad" emitter case, but in the particular use case I had, it was undesirable behavior. 