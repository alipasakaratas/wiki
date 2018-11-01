<a name="top" />

[HOME](Home) » [SNOWPLOW SETUP GUIDE](Setting-up-Snowplow) » [Step 4: setting up alternative data stores](Setting-up-alternative-data-stores) » Setting up Google Cloud Storage loader

## Overview

Refer to the [[Cloud Storage Loader]] document.
You can find the code [on GitHub][csl].

## Configuration

### Cloud Storage Loader specific options

- `--inputSubscription=String`
   The Cloud Pub/Sub subscription to read from, formatted as projects/[PROJECT]/subscriptions/[SUB]
- `--outputDirectory=String`
   The Cloud Storage directory to output files to, ends with /
- `--outputFilenamePrefix=String`
   Default: output
   The Cloud Storage prefix to output files to
- `--shardTemplate=String`
   Default: -W-P-SSSSS-of-NNNNN
   The shard template which will be part of the filenames
- `--outputFilenameSuffix=String`
   Default: .txt
   The suffix of the filenames written out
- `--windowDuration=Int`
   Default: 5
   The window duration in minutes
- `--compression=String`
   Default: none
   The compression used (gzip, bz2 or none), bz2 can't be loaded into BigQuery
- `--numShards=int`
   Default: 1
   The maximum number of output shards produced when writing

### Dataflow options

To run on Dataflow, Beam Enrich will rely on a set of additional configuration options:

- `--runner=DataFlowRunner` which specifies that we want to run on Dataflow
- `--project=[PROJECT]`, the name of the GCP project
- `--streaming=true` to notify Dataflow that we're running a streaming application
- `--zone=europe-west2-a`, the zone where the Dataflow nodes (effectively [GCP Compute Engine](https://cloud.google.com/compute/) nodes) will be launched
- `--region=europe-west2`, the region where the Dataflow job will be launched
- `--gcpTempLocation=gs://[BUCKET]/`, the GCS bucket where temporary files necessary to run the job (e.g. JARs) will be stored

The list of all the options can be found at https://cloud.google.com/dataflow/pipelines/specifying-exec-params#setting-other-cloud-pipeline-options.

## Running

Cloud Storage Loader comes as a ZIP archive, a Docker image or a [Cloud Dataflow template][template],
feel free to choose the one which fits your use case the most.

### Template

You can run Dataflow templates using a variety of means:

- Using the GCP console
- Using `gcloud`
- Using the REST API

Refer to [the documentation on executing templates][executing-templates] to know more.

Here, we provide an example using `gcloud`:

```bash
gcloud dataflow jobs run [JOB-NAME] \
  --gcs-location gs://snowplow-hosted-assets/4-storage/cloud-storage-loader/0.1.0/CloudStorageLoaderTemplate-0.1.0 \
  --parameters \
    inputSubscription=projects/[PROJECT]/subscriptions/[SUBSCRIPTION],\
    outputDirectory=gs://[BUCKET]/YYYY/MM/dd/HH/,\ # partitions by date
    outputFilenamePrefix=output,\ # optional
    shardTemplate=-W-P-SSSSS-of-NNNNN,\ # optional
    outputFilenameSuffix=.txt,\ # optional
    windowDuration=5,\ # optional, in minutes
    compression=none,\ # optional, gzip, bz2 or none
    numShards=1 # optional
```

### ZIP archive


You can find the archive hosted on [our Bintray][bintray].

Once unzipped the artifact can be run as follows:

```bash
./bin/cloud-storage-loader \
  --runner=DataFlowRunner \
  --project=[PROJECT] \
  --streaming=true \
  --zone=europe-west2-a \
  --inputSubscription=projects/[PROJECT]/subscriptions/[SUBSCRIPTION] \
  --outputDirectory=gs://[BUCKET]/YYYY/MM/dd/HH/ \ # partitions by date
  --outputFilenamePrefix=output \ # optional
  --shardTemplate=-W-P-SSSSS-of-NNNNN \ # optional
  --outputFilenameSuffix=.txt \ # optional
  --windowDuration=5 \ # optional, in minutes
  --compression=none \ # optional, gzip, bz2 or none
  --numShards=1 # optional
```

To display the help message:

```bash
./bin/cloud-storage-loader --help
```

To display documentation about Cloud Storage Loader-specific options:

```bash
./bin/cloud-storage-loader --help=com.snowplowanalytics.storage.cloudstorage.loader.Options
```

### Docker image


You can also find the imageon [our Bintray][bintray-docker].

A container can be run as follows:

```bash
docker run \
  -e GOOGLE_APPLICATION_CREDENTIALS=/snowplow/config/credentials.json \ # if running outside GCP
  snowplow-docker-registry.bintray.io/snowplow/cloud-storage-loader:0.1.0 \
  --runner=DataFlowRunner \
  --job-name=[JOB-NAME] \
  --project=[PROJECT] \
  --streaming=true \
  --zone=[ZONE] \
  --inputSubscription=projects/[PROJECT]/subscriptions/[SUBSCRIPTION] \
  --outputDirectory=gs://[BUCKET]/YYYY/MM/dd/HH/ \ # partitions by date
  --outputFilenamePrefix=output \ # optional
  --shardTemplate=-W-P-SSSSS-of-NNNNN \ # optional
  --outputFilenameSuffix=.txt \ # optional
  --windowDuration=5 \ # optional, in minutes
  --compression=none \ # optional, gzip, bz2 or none
  --numShards=1 # optional
```

To display the help message:

```bash
docker run snowplow-docker-registry.bintray.io/snowplow/cloud-storage-loader:0.1.0 \
  --help
```

To display documentation about Cloud Storage Loader-specific options:

```bash
docker run snowplow-docker-registry.bintray.io/snowplow/cloud-storage-loader:0.1.0 \
  --help=com.snowplowanalytics.storage.cloudstorage.loader.Options
```

### Additional information

A full list of all the Beam CLI options can be found at:
https://cloud.google.com/dataflow/pipelines/specifying-exec-params#setting-other-cloud-pipeline-options.


## Tests and debugging

### Testing

The tests for this codebase can be run with `sbt test`.

### Debugging

You can run the job locally and experiment with its different parts using the
[SCIO REPL](https://github.com/spotify/scio/wiki/Scio-REPL) by running `sbt repl/run`.

[csl]: https://github.com/snowplow-incubator/snowplow-cloud-storage-loader/
[template]: https://cloud.google.com/dataflow/docs/templates/overview
[executing-templates]: https://cloud.google.com/dataflow/docs/templates/executing-templates
[bintray]: https://bintray.com/snowplow/snowplow-generic/snowplow-cloud-storage-loader
[bintray-docker]: https://bintray.com/snowplow/registry/snowplow%3Asnowplow-google-cloud-storage-loader
