[**HOME**](Home) > [**SNOWPLOW TECHNICAL DOCUMENTATION**](Snowplow-technical-documentation) > [**Storage**](Storage) > [[Snowplow Google Cloud Storage Loader]]

## Overview

Cloud Storage Loader is a [Dataflow][dataflow] job which dumps event from an input
[PubSub][pubsub] subscription into a [Cloud Storage][storage] bucket.

## Technical details

Cloud Storage loader is built on top of [Apache Beam](https://beam.apache.org/) and its Scala wrapper
[SCIO](https://github.com/spotify/scio).

It groups messages from an input PubSub subscription into configurable windows and write them out
to Google Cloud Storage.

It also optionally partitions the output by date so that you can easily see what was outputted when
in your cloud storage bucket, for example we could see the following hierarchy:

- gs://bucket/
  - 2018
    - 10
      - 31
        - 18
        - 19
        - 20

It can also compress the output data. For now, the supported output compressions are:

- gzip
- bz2
- none

Do note, however, that bz2-compressed data cannot be loaded directly into BigQuery.

For now, it only runs on [GCP's Dataflow][dataflow].

# See also:

+ [Setup guide][setup]
+ [Repository][csl]

[setup]: https://github.com/snowplow/snowplow/wiki/setting-up-snowplow-google-cloud-storage-loader
[csl]: https://github.com/snowplow-incubator/snowplow-google-cloud-storage-loader/
[dataflow]: https://cloud.google.com/dataflow/
[pubsub]: https://cloud.google.com/pubsub/
[storage]: https://cloud.google.com/storage/

