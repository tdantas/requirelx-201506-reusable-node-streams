# Node Streams 101

* Read stream
* Write stream
* Duplex stream
* Pipe
* Transform stream


# Read Stream

Source of data


## Examples

* file
* socket
* server-side HTTP request body
* client-side HTTP response body
* sensor
* process stdout
* ...


## Two modes

* flowing
* non-flowing


## Two modes

* push stream
* pull stream


## Push stream

![Push stream](images/push-stream.gif)

```js
pistol.on('data', function(squirt) {
   console.log('got water!', squirt);
});
```


## Pull stream

![Pull stream](images/pull-stream.jpg)

```js
straw.on('readable', function() {
  var gulp;

  while(gulp = straw.read()) {
    console.log('got gulp!', gulp);
  }
});
```


## Life cycle of a Read Stream 

* Starts paused
* You can resume it by enabling flowing mode
* Otherwise it's on paused mode, you have to explicitely `read()` from the stream
* Emits `end` event
* *May* emit `close` event, depending on type of stream
* No more data after this


## Buffering

The amount of internal buffering a readable stream does is controllable by the `highWaterMark` option.

Defaults to 16kb or 16 objects (when in object mode).


## Encoding

A readable stream emits buffers or strings (unless when in object mode).

If you set the encoding (`options.encoding` or `.setEncoding()`), it emits strings and handles multi-byte characters properly.

```js
source.setEncoding('utf8');

source.on('data', function(data) {
  assert.equal(typeof data, 'string');
});
```


## Object mode

When in object mode, a read stream emits objects.

```js
parser.on('data', function(data) {
  assert.equal(typeof data, 'object');
});
```


## Write stream

Target of data.

(the dog's stomach)


## Examples

* file
* socket
* server-side HTTP response body
* client-side HTTP request body
* process stdin
* ...


## Writing

Write chunk or object:

```js
target.write(chunk);
```


## Writing

Write encoded chunk:

```js
target.write(chunk, encoding);
```


## Writing

Be notified when data is flushed (to underlying resource):

```js
target.write(chunk, encoding, callback);

function callback() {
  console.log('this piece of data was flushed');
}
```


## Flushed or not flushed

`stream.write()` returns boolean, `true` if the data was flushed.

```js
var shouldContinueWriting = stream.write(chunk);
```


## Flushed or not flushed

Stream emits `drain` when it's appropriate to write more:

```js
target.on('drain', function() {
  console.log('now I can write more'); 
});
```


## Ending

You can end a write stream:

```js
target.end();
```

or, with a last chunk:

```js
target.end(chunk);
```


## Finishing

After ending it and all data has been flushed, a writable stream emits the `finish` event:

```js
target.write(chunk1);
target.write(chunk2);
target.end();

target.once('finish', function() {
  console.log('all writes are now complete!');
});
```