+++
title = "API Reference"
+++


## API Reference

### List all index schemas

`GET /index`

Returns the schema of all indexes in JSON.

Request:
```
curl -XGET localhost:10101/index
```

Response:
```
{"indexes":[{"name":"user","frames":[{"name":"collab"}]}]}
```

### List index schema

`GET /index/<index-name>`

Returns the schema of the specified index in JSON.

Request:
```
curl -XGET localhost:10101/index/user
```

Response:
```
{"index":{"name":"user"}, "frames":[{"name":"collab"}]}]}
```

### Create index

`POST /index/<index-name>`

Creates an index with the given name.

The request payload is in JSON, and may contain the `options` field. The `options` field is a JSON object which may contain the following fields:

* `columnLabel` (string): column label of the index.

Request:
```
curl localhost:10101/index/user \
     -X POST \
     -d '{"options": {"columnLabel": "user_id"}}'
```

Response:
```
{}
```

### Remove index

`DELETE /index/index-name`

Removes the given index.

Request:
```
curl -XDELETE localhost:10101/index/user
```

Response:
```
{}
```

### Query index

`POST /index/<index-name>/query`

Sends a query to the Pilosa server with the given index. The request body is UTF-8 encoded text and response body is in JSON by default.

Request:
```
curl localhost:10101/index/user/query \
     -X POST \
     -d 'Bitmap(frame="language", id=5)'
```

Response:
```
{"results":[{"attrs":{},"bits":[100]}]}
```

In order to send protobuf binaries in the request and response, set `Content-Type` and `Accept` headers to: `application/x-protobuf`.

The response doesn't include column attributes by default. To return them, set the `columnAttrs` query argument to `true`.

The query is executed for all [slices](../data-model#slice) by default. To use specified slices only, set the `slices` query argument to a comma-separated list of slice indices.

Request:
```
curl "localhost:10101/index/user/query?columnAttrs=true&slices=0,1" \
     -X POST \
     -d 'Bitmap(frame="language", id=5)'
```
Response:
```
{
  "results":[{"attrs":{},"bits":[100]}],
  "columnAttrs":[{"id":100,"attrs":{"name":"Klingon"}}]
}
```

### Change index time quantum

`PATCH /index/<index-name>/time-quantum`

Changes the time quantum for the given index. This endpoint should be called at most once right after creating a database.

The payload is in JSON with the format: `{"timeQuantum": "${TIME_QUANTUM}"}`. Valid time quantum values are:

* (Empty string)
* Y: year
* M: month
* D: day
* H: hour
* YM: year and month
* MD: month and day
* DH: day and hour
* YMD: year, month and day
* MDH: month, day and hour
* YMDH: year, month, day and hour

Request:
```
curl localhost:10101/index/user/time-quantum \
     -X POST \
     -d '{"timeQuantum": "YM"}'
```

Response:
```
{}
```

### Create frame

`POST /index/<index-name>/frame/<frame-name>`

Creates a frame in the given index with the given name.

The request payload is in JSON, and may contain the `options` field. The `options` field is a JSON object which may contain the following fields:

* `rowLabel` (string): Row label of the frame.
* `timeQuantum` (string): [Time Quantum]({{< ref "data-model.md#time-quantum" >}}) for this frame.
* `inverseEnabled` (boolean): Enables [the inverted view]({{< ref "data-model.md#inverse" >}}) for this frame if `true`.
* `cacheType` (string): [ranked]({{< ref "data-model.md#ranked" >}}) or [LRU]({{< ref "data-model.md#lru" >}}) caching on this frame. Default is `lru`.
* `cacheSize` (int): Number of rows to keep in the cache. Default 50,000.

Request:
```
curl localhost:10101/index/user/frame/language \
     -X POST \
     -d '{"options": {"rowLabel": "language_id"}}'
```

Response:
```
{}
```

### Remove frame

`DELETE POST /index/<index-name>/frame/<frame-name>`

Removes the given frame.

Request:
```
curl -XDELETE localhost:10101/index/user/frame/language
```

Response:
```
{}
```

### Change frame time quantum

`PATCH /index/<index-name>/frame/<frame-name>/time-quantum`

Changes the time quantum for the given frame. This endpoint should be called at most once right after creating a frame.

The payload is in JSON with the format: `{"timeQuantum": "${TIME_QUANTUM}"}`. Valid time quantum values are:

* (Empty string)
* Y: year
* M: month
* D: day
* H: hour
* YM: year and month
* MD: month and day
* DH: day and hour
* YMD: year, month and day
* MDH: month, day and hour
* YMDH: year, month, day and hour

Request:
```
curl localhost:10101/index/user/frame/language/time-quantum \
     -X POST \
     -d '{"timeQuantum": "YM"}'
```

Response:
```
{}
```

### List hosts

`GET /hosts`

Returns the hosts in the cluster.

Request:
```
curl -XGET localhost:10101/hosts
```

Response:
```
[{"host":":10101","internalHost":""}]
```

### Get version

`GET /version`

Returns the version of the Pilosa server.

Request:
```
curl -XGET localhost:10101/version
```

Response:
```
{"version":"v0.4.0"}
```

