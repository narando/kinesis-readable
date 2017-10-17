# kinesis-readable

[![Build Status](https://travis-ci.org/rclark/kinesis-readable.svg?branch=master)](https://travis-ci.org/rclark/kinesis-readable)

Node.js stream interface for reading records from [AWS Kinesis](http://aws.amazon.com/kinesis/).

## Usage

```js
var AWS = new AWS.Kinesis({
  region: 'us-east-1',
  params: { StreamName: 'my-stream' }
});

// see below for options
var readable = require('kinesis-readable')(client, options);

readable
  // 'data' events will trigger for a set of records in the stream
  .on('data', function(records) {
    console.log(records);
  })
  // each time a records are passed downstream, the 'checkpoint' event will provide
  // the last sequence number that has been read
  .on('checkpoint', function(sequenceNumber) {
    console.log(sequenceNumber);
  })
  .on('error', function(err) {
    console.error(err);
  })
  .on('end', function() {
    console.log('all done!');
  });

// Calling .close() will finish all pending GetRecord requests before emitting
// the 'end' event.
// Because the kinesis stream persists, the readable stream will not
// 'end' until you explicitly close it
setTimeout(function() {
  readable.close();
}, 60 * 60 * 1000);
```

## Options

You can pass options to create the readable stream, all parameters are optional:

```js
var options = {
  shardId: 'shard-identifier', // defaults to first shard in the stream
  iterator: 'LATEST', // default to TRIM_HORIZON
  startAfter: '12345678901234567890', // start reading after this sequence number
  startAt: '12345678901234567890', // start reading from this sequence number
  timestamp: '2016-04-04T19:58:46.480-00:00', // start reading from this timestamp
  limit: 100 // number of records per `data` event
};
```
