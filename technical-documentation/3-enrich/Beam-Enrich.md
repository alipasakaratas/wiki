[**HOME**](Home) > [**SNOWPLOW TECHNICAL DOCUMENTATION**](Snowplow-technical-documentation) > [**Enrichment**](Enrichment) > [[Beam Enrich]]

## Overview

Beam Enrich is the latest real-time enrichment platform developed at Snowplow Analytics.
It takes as input a stream of raw data collected by the [[Scala Stream Collector]], enrich it
(using [scala-common-enrich][common-enrich]), and outputs both a stream of successfully enriched
events as well as a stream of events that have failed enrichment.

## Technical details

Beam Enrich is built on top of [Apache Beam](https://beam.apache.org/) and its Scala wrapper
[SCIO](https://github.com/spotify/scio).

It enriches the raw data, using [scala-common-enrich][common-enrich] outputted by the
[[Scala Stream Collector]] in a [GCP PubSub topic](https://cloud.google.com/pubsub/) and outputs
both the successfully enriched and those who failed enrichments to their respective PubSub topics.

For now, it only runs on [GCP's Dataflow](https://cloud.google.com/dataflow/).

## Beam Enrich output

Beam Enrich turns the raw events into TSV enriched events following our
[[Canonical event model]] that are ready to be fed into
[the BigQuery Loader](https://github.com/snowplow-incubator/snowplow-bigquery-loader) which takes
care of actually loading the data into [BigQuery](https://cloud.google.com/bigquery/).

# See also:

+ [Setup guide][setup]
+ [Repository][beam-enrich]

[common-enrich]: https://github.com/snowplow/snowplow/tree/master/3-enrich/scala-common-enrich

[setup]: https://github.com/snowplow/snowplow/wiki/setting-up-beam-enrich
[beam-enrich]: https://github.com/snowplow/snowplow/tree/master/3-enrich/beam-enrich
