# node-thrift

Thrift protocol implementation for nodejs. As of version 0.0.1, the basic
protocol has been implemented. A Thrift compiler that will generate the .js
files from a Thrift specification is being implemented as well, see the
Thrift Compiler section below.

An example of one of the Cassandra methods is included in the examples/
folder. This example was first generated by the upstream Thrift JavaScript
generator, and then modified to accept a callback and work properly in a
fully async environment. 

NOTE: you must use the framed thrift transport, TFramedTransport in most
implementations, on the server side. Using a popular example, this is enabled
by default in Cassandra 0.7 (but configuration must be changed in Cassandra
0.6.x and earlier).

## Thrift Compiler

A Thrift compiler is being built in a forked version of the upstream thrift
library. You can check it out here:
[http://github.com/wadey/thrift](http://github.com/wadey/thrift)

Once you build this patched version of Thrift, you can compile nodejs sources
by running the following:

    thrift --gen js:node thrift_file

## Usage:

Here is a Cassandra example:

    var thrift = require('thrift'),
        Cassandra = require('./examples/Cassandra')
        ttypes = require('./examples/cassandra_types');

    var connection = thrift.createConnection("localhost", 9160),
        client = thrift.createClient(Cassandra.Client, connection);

    connection.on('error', function(err) {
      console.error(err);
    });

    client.get_slice("Keyspace", "key", new ttypes.ColumnParent({column_family: "ExampleCF"}), new ttypes.SlicePredicate({slice_range: new ttypes.SliceRange({start: '', finish: ''})}), ttypes.ConsistencyLevel.ONE, function(err, data) {
      if (err) {
        // handle err
      } else {
        // data == [ttypes.ColumnOrSuperColumn, ...]
      }
      connection.end();
    });
