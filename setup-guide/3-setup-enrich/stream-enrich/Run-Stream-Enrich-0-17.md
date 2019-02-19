<a name="top" />

[HOME](Home) » [SNOWPLOW SETUP GUIDE](Setting-up-Snowplow) » [Step 3: Setting up Enrich](Setting-up-enrich) » [Step 3.2: setting up Stream Enrich](setting-up-stream-enrich) » [[Install Stream Enrich]] » [[Configure Stream Enrich]] » Run Stream Enrich

**This documentation is for version 0.17.x of Stream Enrich. Documentation for other versions is available:**

- [Stream Enrich v0.10.0 - v0.14.0][v014]
- [Stream Enrich v0.5.0 - v0.10.0][v010]

## Running

Stream Enrich is a jarfile. Simply provide the configuration file as a parameter:

    $ java -jar snowplow-stream-enrich-[targeted platform]-[version].jar --config my.conf --resolver file:resolver.json

Where targeted platform can be one of:

- kinesis
- google-pubsub
- kafka
- nsq
- stdin

This will start the Stream Enrich app to read raw events from Kinesis, Google PubSub, Kafka, NSQ or
stdin depending on the targeted platform chosen and write enriched events back to Kinesis, Google
Pubsub, Kafka, NSQ, or stdout.

If you are using configurable enrichments, provide the path to your enrichments directory as a
parameter:

    $ java -jar snowplow-stream-enrich-[targeted platform]-[version].jar --config my.conf --resolver file:resolver.js --enrichments file:path/to/enrichments

If you are using Kinesis and storing the resolver and/or enrichments in DynamoDB, use the
"dynamodb:" prefix in place of the "file:" prefix:

    $ java -jar snowplow-stream-enrich-kinesis-[version].jar --config my.conf --resolver dynamodb:eu-west-1/ConfigurationTable/resolver --enrichments dynamodb:eu-west-1/ConfigurationTable/enrichment

The above command assumes that the enrichments and resolver are stored in a table named
`ConfigurationTable` in eu-west-1, that the key for that table is `id`, that the resolver JSON is
stored in an item whose key has value `resolver`, and the enrichments are stored in items whose
keys have values beginning with "enrichment".

If you are using Google PubSub and storing the resolver and/or enrichments in Datastore, use the
"datastore:" prefix in place of the "file:" prefix:

    $ java -jar snowplow-stream-enrich-google-pubsub-[version].jar --config my.conf --resolver datastore:resolver/iglu --enrichments datastore:enrichment/enrich-

The above command assumes that the resolver has kind `resolver` and has `iglu` as key, the value
being stored in column "json". It also assumes that the enrichments have kind `enrichment` and their
names start with `enrich-` with their values being stored in column "json".

### Configuring the log level

Stream Enrich uses [slf4j logging][logging]:

    $ java -Dorg.slf4j.simpleLogger.defaultLogLevel=debug \
        -jar snowplow-stream-enrich-[version].jar --config my.conf --resolver file:resolver.json

This will also affect messages logged by the [Kinesis Client Library][kcl] (which Stream Enrich uses
if you're using Kinesis)

## All done?

You have setup Stream Enrich! You are now ready to [setup alternative data stores](Setting-up-alternative-data-stores).

Return to the [setup guide](Setting-up-Snowplow).

[v010]: https://github.com/snowplow/snowplow/wiki/Run-Stream-Enrich-0-10.md
[v014]: https://github.com/snowplow/snowplow/wiki/Run-Stream-Enrich-0-14.md

[logging]: http://www.slf4j.org/api/org/slf4j/impl/SimpleLogger.html
[kcl]: https://github.com/awslabs/amazon-kinesis-client

[scala-out]: https://github.com/snowplow/snowplow/wiki/Configure-the-Scala-Stream-Collector#2-sinks
[scala-template]: https://github.com/snowplow/snowplow/wiki/Configure-the-Scala-Stream-Collector#template
[kinesis-in]: https://github.com/snowplow/snowplow/wiki/Configure-Scala-Kinesis-Enrich#source
[kinesis-template]: https://github.com/snowplow/snowplow/wiki/Configure-Scala-Kinesis-Enrich#template
