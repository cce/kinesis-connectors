# Golang Kinesis Connectors

__Kinesis connector applications written in Go__

> With the new release of Kinesis Firehose I'd recommend using the [Lambda Streams to Firehose](https://github.com/awslabs/lambda-streams-to-firehose) project for loading data directly into S3 and Redshift.

Inspired by the [Amazon Kinesis Connector Library](https://github.com/awslabs/amazon-kinesis-connectors). This library is intended to be a lightweight wrapper around the Kinesis API to handle batching records, setting checkpoints, respecting ratelimits,  and recovering from network errors.

![golang_kinesis_connector](https://cloud.githubusercontent.com/assets/739782/4262283/2ee2550e-3b97-11e4-8cd1-21a5d7ee0964.png)

## Overview

The consumer expects a handler func that will process a buffer of incoming records.

```go
func main() {
  var(
    app    = flag.String("app", "", "The app name")
    stream = flag.String("stream", "", "The stream name")
  )
  flag.Parse()

  // create new consumer
  c := connector.NewConsumer(connector.Config{
    AppName:        *app,
    MaxRecordCount: 400,
    Streamname:     *stream,
  })

  // process records from the stream
  c.Start(connector.HandlerFunc(func(b connector.Buffer) {
    fmt.Println(b.GetRecords())
  }))

  select {}
}
```

### Config

The default behavior for checkpointing uses Redis on localhost. To set a custom Redis URL use ENV vars:

```
REDIS_URL=my-custom-redis-server.com:6379
```

### Logging

[Apex Log](https://medium.com/@tjholowaychuk/apex-log-e8d9627f4a9a#.5x1uo1767) is used for logging Info. Override the logs format with other [Log Handlers](https://github.com/apex/log/tree/master/_examples). For example using the "json" log handler:

```go
import(
  "github.com/apex/log"
  "github.com/apex/log/handlers/json"
)

func main() {
  // ...

  log.SetHandler(json.New(os.Stderr))
  log.SetLevel(log.DebugLevel)
}
```

Which will producde the following logs:

```
  INFO[0000] processing                app=test shard=shardId-000000000000 stream=test
  INFO[0008] emitted                   app=test count=500 shard=shardId-000000000000 stream=test
  INFO[0012] emitted                   app=test count=500 shard=shardId-000000000000 stream=test
```

### Installation

Get the package source:

    $ go get github.com/harlow/kinesis-connectors

### Fetching Dependencies

Install `govendor`:

    $ export GO15VENDOREXPERIMENT=1
    $ go get -u github.com/kardianos/govendor

Install dependencies into `./vendor/`:

    $ govendor sync

### Examples

Use the [seed stream](https://github.com/harlow/kinesis-connectors/tree/master/examples/seed) code to put sample data onto the stream.

* [Firehose](https://github.com/harlow/kinesis-connectors/tree/master/examples/firehose)
* [S3](https://github.com/harlow/kinesis-connectors/tree/master/examples/s3)

## Contributing

Please see [CONTRIBUTING.md] for more information. Thank you, [contributors]!

[LICENSE]: /MIT-LICENSE
[CONTRIBUTING.md]: /CONTRIBUTING.md

## License

Copyright (c) 2015 Harlow Ward. It is free software, and may
be redistributed under the terms specified in the [LICENSE] file.

[contributors]: https://github.com/harlow/kinesis-connectors/graphs/contributors

> [www.hward.com](http://www.hward.com) &nbsp;&middot;&nbsp;
> GitHub [@harlow](https://github.com/harlow) &nbsp;&middot;&nbsp;
> Twitter [@harlow_ward](https://twitter.com/harlow_ward)
