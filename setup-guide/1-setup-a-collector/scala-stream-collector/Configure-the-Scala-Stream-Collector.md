[HOME](Home) » [SNOWPLOW SETUP GUIDE](Setting-up-Snowplow) » [Step 1: setup a Collector](Setting-up-a-Collector) » [[Setting up the Scala Stream Collector]] » [[Install the Scala Stream Collector]] » Configure the Scala Stream Collector

**This documentation refers to version 0.16.0 of the Scala Stream Collector**

**[Version 0.15.0 & 0.14.0 & 0.13.0 & 0.12.0][v0.15]**
**[Version 0.11.0][v0.11]**
**[Version 0.10.0][v0.10]**

The Scala Stream Collector has a number of configuration options available.

## Basic configuration

### Template

Download a template configuration file from GitHub: [config.hocon.sample][app-conf].

Now open the `config.hocon.sample` file in your editor of choice.

### Stream configuration

You will need to put in the names of your `good` and `bad` streams:

+ `collector.streams.good`: The name of the good input stream of the tool which you choose as a sink. This is stream where the events which have successfully been collected will be stored in.
+ `collector.streams.bad`: The name of the bad input stream of the tool which you choose as a sink. This is stream where the events that are too big (w.r.t Kinesis 1MB limit) will be stored in.
+ `collector.streams.useIpAddressAsPartitionKey`: Whether to use the incoming event's ip as the partition key for the good stream/topic

### HTTP settings

Also verify the settings of the HTTP service:

+ `collector.interface`
+ `collector.port`

### Buffer settings

You will also need to set appropriate limits for:

+ `collector.streams.buffer.byteLimit`
+ `collector.streams.buffer.recordLimit`
+ `collector.streams.buffer.timeLimit`

## Additional configuration options

### 1. Sinks

The `collector.streams.sink.enabled` setting determines which of the supported sinks to write raw
events to:
+ `"kinesis"` for writing Thrift-serialized records and error rows to a Kinesis stream
+ `"kafka"` for writing Thrift-serialized records and error rows to a Kafka topic
+ `"googlepubsub"` for writing Thrift-serialized records and error rows to a Google PubSub topic
+ `"stdout"` for writing Base64-encoded Thrift-serialized records and error rows to stdout and stderr respectively
+ `"nsq"` for writing Thrift-serialized records and error rows to NSQ topic

If you switch to `"stdout"`, we recommend setting 'akka.loglevel = OFF' and 'akka.loggers = []' to prevent Akka debug information from polluting your event stream on stdout.

You should fill the rest of the `collector.streams.sink` section according to your selection as a
sink.

To use `stdout` as a sink comment everything in the `collector.streams.sink` but
`collector.streams.sink.enabled` which should be set to `stdout`.

### 2. Setting the P3P policy header

The P3P policy header is set in `collector.p3p`, and
if not set, the P3P policy header defaults to:

	policyRef="/w3c/p3p.xml", CP="NOI DSP COR NID PSA OUR IND COM NAV STA"

### 3. Setting the domain name

Set the cookie name using the `collector.cookie.name` setting. To maintain backward compatibility with earlier versions of the collector, use the string "sp" as the cookie name.

The collector responds to valid requests with a `Set-Cookie` header, which may or may not specify a `domain` for the cookie.

If no domain is specified, the cookie will be set against the full collector domain, for example `collector.snplow.com`. That will mean that applications running elsewhere on `*.snplow.com` won't be able to access it. If you don't need to grant access to the cookie from other applications on the domain, then you can ignore the `domains` and `fallbackDomain` settings.

In earlier versions, you could specify a `domain` to tie the cookie to. For example, if set to `.snplow.com`, the cookie would have been accessible to other applications running on `*.snplow.com`. To do the same in this version, use the `fallbackDomain` setting but **make sure** that you no longer include a leading dot:

```hocon
fallbackDomain = "snplow.com"
```

The cookie set by the collector can be treated differently by browsers, depending on whether it's considered to be a first-party or a third-party cookie. Up until now, if you had two collector endpoints, one on `collector.snplow.com` and one on `collector.snplow.net`, you could only specify one of those domains in the configuration. That meant that you were only able to set a first-party cookie server-side on either `.snplow.com` or `.snplow.net`, but not on both. From version 0.16.0, you can specify a list of domains to be used for the cookie (**note the lack of a leading dot**):

```hocon
domains = [
  "snplow.com"
  "snplow.net"
]
```

Which domain to be used in the `Set-Cookie` header is determined by matching the domains from the `Origin` header of the request to the specified list. The first match is used. If no matches are found, the fallback domain will be used, if configured. If no `fallbackDomain` is configured, the cookie will be tied to the full collector domain.

If you specify a main domain in the list, all subdomains on it will be matched. If you specify a subdomain, only that subdomain will be matched.

Examples:
- `domain.com` will match `Origin` headers like `domain.com`, `www.domain.com` and `secure.client.domain.com`
- `client.domain.com` will match an `Origin` header like `secure.client.domain.com` but not `domain.com` or `www.domain.com`.

### 4. Configuring `Secure`, `HttpOnly` and `SameSite` attributes for the cookie

You can control the values of those attributes in the `Set-Cookie` response header by specifying them in `collector.cookie`:

```hocon
secure = false    # set to true if you want to enforce secure connections
httpOnly = false  # set to true if you want to make the cookie inaccessible to non-HTTP requests
sameSite = "None" # or "Lax", or "Strict"
```

The `sameSite` parameter is optional. If you omit it, the default value `None` will be assumed.

### 5. Setting the cookie duration

The cookie expiration duration is set in `collector.cookie.expiration`. If no value is provided, cookies set the default to expire after one year (i.e. 365 days). If you don't want to set a third party cookie at all it could be disabled by setting `collector.cookie.enabled` to `false`. Alternatively, it could be achieved if `collector.cookie.expiration` is set to 0 (from version 0.4.0).

### 6. Configuring custom paths

The collector responds with a cookie to requests with a path that matches the `vendor/version` protocol. The expected values are:
- `com.snowplowanalytics.snowplow/tp2` for Tracker Protocol 2
- `r/tp2` for redirects
- `com.snowplowanalytics.iglu/v1` for the Iglu Webhook.

You can also map any valid (ie, two-segment) path to one of the three defaults via the `collector.paths` section of the configuration file. Your custom path must be the key and the value must be one of the corresponding default paths. Both must be full valid paths starting with a leading slash:

```hocon
paths {
  "/com.acme/track"    = "/com.snowplowanalytics.snowplow/tp2"
  "/com.acme/redirect" = "/r/tp2"
  "/com.acme/iglu"     = "/com.snowplowanalytics.iglu/v1"
}
```

» Next: [[Run the Scala Stream collector]]

[v0.10]: https://github.com/snowplow/snowplow/wiki/Configure-the-Scala-Stream-Collector-v0.10
[v0.11]: https://github.com/snowplow/snowplow/wiki/Configure-the-Scala-Stream-Collector-v0.11
[v0.15]: https://github.com/snowplow/snowplow/wiki/Configure-the-Scala-Stream-Collector-v0.15
[app-conf]: https://raw.githubusercontent.com/snowplow/snowplow/master/2-collectors/scala-stream-collector/examples/config.hocon.sample
