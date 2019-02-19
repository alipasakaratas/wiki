To simplify setting up and running Snowplow, the Snowplow Analytics team provide public hosting for some of the Snowplow sub-components. These hosted assets are publically available through Amazon Web Services (CloudFront and S3), and using them is free for Snowplow community members.

As we release new versions of these assets, we will leave old versions unchanged on their existing URLs - so you won't have to upgrade your own Snowplow installation unless you want to.

**Disclaimer: While Snowplow Analytics Ltd will make every reasonable effort to host these assets, we will not be liable for any failure to provide this service. All of the hosted assets listed below are freely available via [our GitHub repository][snowplow-repo] and you are encouraged to host them yourselves.**

The **current versions** of the assets hosted by the Snowplow Analytics team are as follows:

## 0. Snowplow CLI

We are steadily moving over to [Bintray][bintray] for hosting binaries and artifacts which don't have to be hosted on S3.

To make operating Snowplow easier, the EmrEtlRunner app are now available as prebuilt executables in a single zipfile here:

    http://dl.bintray.com/snowplow/snowplow-generic/snowplow_emr_r109_lambaesis.zip

Right-click on this [Download link][emr-download] to save it down locally.

**Note**: The link above refers to the latest version at the time of writing (R109). If you know there is a newer version you can locate and download it from the [generic page](http://dl.bintray.com/snowplow/snowplow-generic/). Search for the pattern `snowplow_emr_`. The higher the number version the newer it is.

## 1. Trackers

### 1.1 JavaScript Tracker resources

The minified JavaScript tracker is hosted on CloudFront against its full semantic version:

    http(s)://d1fc8wv8zag5ca.cloudfront.net/2.9.0/sp.js

**Note**: The above URL references JavaScript tracker v2.8.2 (d1fc8wv8zag5ca.cloudfront.net/**2.8.2**/sp.js). To ensure you are using the latest version, please, check what it currently is at [GitHub](https://github.com/snowplow/snowplow-javascript-tracker/releases) and amend accordingly.

## 2. Collectors

### 2.1 Clojure Collector resources

The Clojure Collector packaged as a complete WAR file, ready for Amazon Elastic Beanstalk, is here:

    s3://snowplow-hosted-assets/2-collectors/clojure-collector/clojure-collector-2.1.2-standalone.war

Right-click on this [Download link][cc-download] to save it down locally via CloudFront CDN.

### 2.2 Scala Stream Collector resources

The Scala Stream Collectors are available on Bintray here:

    https://bintray.com/snowplow/snowplow-generic/snowplow-scala-stream-collector/0.15.0#files

Choose an artifact according to the supported targeted platform:

- Kinesis
- Google PubSub
- Kafka
- NSQ

## 3. Enrich

### 3.1 Spark Enrich resources

The Spark Enrich process uses a single jarfile containing the Spark job. This is made available in a
public Amazon S3 bucket, for Snowplowers who are running their Spark Enrich process on Amazon EMR:

    s3://snowplow-hosted-assets/3-enrich/spark-enrich/snowplow-spark-enrich-1.17.0.jar

Right-click on this [Download link][spark-enrich-download] to save it down locally via CloudFront CDN.

### 3.2 Stream Enrich resources

The Stream Enrich app is available on Bintray here:

    https://bintray.com/snowplow/snowplow-generic/snowplow-stream-enrich/0.20.0#files

Choose an artifact according to the supported targeted platform:

- Kinesis
- Kafka
- NSQ

### 3.3 Scala Hadoop Event Recovery resources

The Scala Hadoop Event Recovery (formerly Hadoop Bad Rows) tool uses a single jarfile containing the MapReduce job. This is made available in a public Amazon S3 bucket:

    s3://snowplow-hosted-assets/3-enrich/hadoop-event-recovery/snowplow-hadoop-event-recovery-0.2.0.jar

Right-click on this [Download link][hadoop-event-recovery-download] to save it down locally via CloudFront CDN.

### 3.4 Shared resources

#### 3.4.1 MaxMind GeoLiteCity

The [ip lookups enrichment](IP-lookups-enrichment) makes use of the free [GeoLite City database][geolite] from [MaxMind, Inc][maxmind], also stored in this public Amazon S3 bucket:

    s3://snowplow-hosted-assets/third-party/maxmind/GeoLite2-City.mmdb

This file is updated every month by the Snowplow Analytics team.
See [this table](#7.-S3-hosted-asset-bucket-per-region) for your bucket.

#### 3.4.1 User-Agent parser database

[The UA parser enrichment](ua-parser-enrichment) makes use of the [uap-core database](https://github.com/ua-parser/uap-core/), also stored in this public Amazon S3 bucket:

    s3://snowplow-hosted-assets/third-party/ua-parser/regexes.yaml

This file is updated every month by the Snowplow Analytics team.
See [this table](#7.-S3-hosted-asset-bucket-per-region) for your bucket.

## 4. Storage

### 4.1 Relational Database Shredder resources

The Relational Database Shredder process uses a single jarfile containing the Spark job. This is
made available in a public Amazon S3 bucket, for Snowplowers who are running their Spark Enrich &
Shred process on Amazon EMR:

    s3://snowplow-hosted-assets/4-storage/rdb-shredder/snowplow-rdb-shredder-0.13.0.jar

Right-click on this [Download link][rdb-download] to save it down locally via CloudFront CDN.

### 4.2 Redshift Storage resources

Our shredding process for loading JSONs into Redshift uses a standard set of JSON Path files, available here:

    s3://snowplow-hosted-assets/4-storage/redshift-storage/jsonpaths

If you are running RDB Loader, these files will automatically be used for loading corresponding JSONs into Redshift.

### 4.3 Elasticsearch Loader resources

The Elasticsearch Loader app is available for both Elasticsearch APIs (HTTP and transport) on Bintray here:

    http://dl.bintray.com/snowplow/snowplow-generic/snowplow_elasticsearch_loader_http_0.10.1.zip
    http://dl.bintray.com/snowplow/snowplow-generic/snowplow_elasticsearch_loader_tcp_0.10.1.zip
    http://dl.bintray.com/snowplow/snowplow-generic/snowplow_elasticsearch_loader_tcp_2x_0.10.1.zip

Right-click on:

  - this [Download link][esl-dl-http] for the version using the HTTP API which works with
every Elasticsearch version
  - this [Download link][esl-dl-tcp] for the version using the transport API for 5.x clusters
  - this [Download link][esl-dl-tcp-2x] for the version using the transport API for 2.x clusters

### 4.4 Snowplow S3 Loader resources

The Snowplow S3 Loader app is available for download separately here:

    http://dl.bintray.com/snowplow/snowplow-generic/snowplow_s3_loader_0.6.0.zip

Right-click on this [Download link][s3-loader-download] to save it down locally.

## 5. Analytics

No hosted assets currently.

## 6. Relays (AWS Lambda)

You can find the jars for our relays in our public S3 buckets close to your region (see [this table](#7.-S3-hosted-asset-bucket-per-region) for your bucket) 

### 6.1 Snowplow Piinguin Relay

You can find the Snowplow Piinguin Relay in an S3 bucket in your region (see [this table](#7.-S3-hosted-asset-bucket-per-region) for your bucket)

For instance for `eu-west-1` the asset will be at: 
`s3://snowplow-hosted-assets/relays/piinguin/snowplow-piinguin-relay-0.1.0.jar`

### 6.2 Snowplow Indicative Relay

You can find the Snowplow Indicative Relay in an S3 bucket in your region (see [this table](#7.-S3-hosted-asset-bucket-per-region) for your bucket)

For instance for `eu-west-1` the asset will be at: 
`s3://snowplow-hosted-assets/relays/indicative/indicative-relay-0.1.0.jar`

## 7. S3 hosted asset bucket per region

| Region | Bucket |
|--------|--------|
|eu-west-1|snowplow-hosted-assets|
|us-east-1|snowplow-hosted-assets-us-east-1|
|us-west-1|snowplow-hosted-assets-us-west-1|
|us-west-2|snowplow-hosted-assets-us-west-2|
|sa-east-1|snowplow-hosted-assets-sa-east-1|
|eu-central-1|snowplow-hosted-assets-eu-central-1|
|ap-southeast-1|snowplow-hosted-assets-ap-southeast-1|
|ap-southeast-2|snowplow-hosted-assets-ap-southeast-2|
|ap-northeast-1|snowplow-hosted-assets-ap-northeast-1|
|ap-south-1|snowplow-hosted-assets-ap-south-1|
|us-east-2|snowplow-hosted-assets-us-east-2|
|ca-central-1|snowplow-hosted-assets-ca-central-1|
|eu-west-2|snowplow-hosted-assets-eu-west-2|
|ap-northeast-2|snowplow-hosted-assets-ap-northeast-2|


## See also

As well as these hosted assets for running Snowplow, the Snowplow Analytics team also make code components and libraries available through Ruby and Java artifact repositories.

Please see the [[Artifact repositories]] wiki page for more information.

[snowplow-repo]: https://github.com/snowplow/snowplow
[cc-download]: http://d2io1hx8u877l0.cloudfront.net/2-collectors/clojure-collector/clojure-collector-2.1.2-standalone.war
[spark-enrich-download]: http://d2io1hx8u877l0.cloudfront.net/3-enrich/spark-enrich/snowplow-spark-enrich-1.17.0.jar
[rdb-download]: http://d2io1hx8u877l0.cloudfront.net/4-storage/rdb-shredder/snowplow-rdb-shredder-0.13.0.jar
[hadoop-event-recovery-download]: http://d2io1hx8u877l0.cloudfront.net/3-enrich/hadoop-event-recovery/snowplow-hadoop-event-recovery-0.2.0.jar
[glc-download]: http://d2io1hx8u877l0.cloudfront.net/third-party/maxmind/GeoLite2-City.mmdb
[geolite]: https://dev.maxmind.com/geoip/geoip2/geolite2/?rld=snowplow
[maxmind]: http://www.maxmind.com/?rld=snowplow

[bintray]: https://bintray.com/
[s3-loader-download]: http://dl.bintray.com/snowplow/snowplow-generic/snowplow_s3_loader_0.6.0.zip
[esl-dl-http]: http://dl.bintray.com/snowplow/snowplow-generic/snowplow_elasticsearch_loader_http_0.10.1.zip
[esl-dl-tcp]: http://dl.bintray.com/snowplow/snowplow-generic/snowplow_elasticsearch_loader_tcp_0.10.1.zip
[esl-dl-tcp-2x]: http://dl.bintray.com/snowplow/snowplow-generic/snowplow_elasticsearch_loader_tcp_2x_0.10.1.zip
[emr-download]: http://dl.bintray.com/snowplow/snowplow-generic/snowplow_emr_r108_val_camonica.zip
