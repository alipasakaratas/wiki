<a name="top" />

[HOME](Home) » [SNOWPLOW SETUP GUIDE](Setting-up-Snowplow) » [Step 3: Setting up Enrich](Setting-up-enrich) » Step 3.2: Setting up Beam Enrich

[[https://d3i6fms1cm1j0i.cloudfront.net/github-wiki/images/3-enrich.png]]

## Overview of Beam Enrich

Refer to the [[Beam Enrich]] document.
You can find the code [on GitHub][beam-enrich].

## Configuration

### Beam Enrich specific options

Beam Enrich comes with a set of predefined CLI options:

- `--job-name`, the name of the job as it will appear in the Dataflow console
- `--raw=projects/{project}/subscriptions/{raw-topic-subscription}` which describes the input PubSub subscription Beam Enrich will
consume from
- `--enriched=projects/{project}/topics/{enriched-topic}` which is the PubSub topic the successfully enriched events will be sinked to
- `--bad=projects/{project}/topics/{bad-topic}`, the PubSub topic where events that have failed enrichment will end up
- `--pii=projects/{project}/topics/{pii-topic}`, the PubSub topic where events resulting from the PII enrichment will end up, optional
- `--resolver=iglu_resolver.json`, the necessary Iglu resolver to lookup the schemas in your data
- `--enrichments=enrichments` the optional directory containing the enrichments that need to be performed

It's important to note that every enrichment relying on local files will need to have the necessary
files stored in [Google Cloud Storage](https://cloud.google.com/storage/), e.g. the IP lookups enrichment:

```json
{
  "schema": "iglu:com.snowplowanalytics.snowplow/ip_lookups/jsonschema/2-0-0",
  "data": {
    "name": "ip_lookups",
    "vendor": "com.snowplowanalytics.snowplow",
    "enabled": true,
    "parameters": {
      "geo": {
        "database": "GeoLite2-City.mmdb",
        "uri": "gs://gcs-bucket/maxmind"
      }
    }
  }
}
```

### Dataflow options

To run on Dataflow, Beam Enrich will rely on a set of additional configuration options:

- `--runner=DataFlowRunner` which specifies that we want to run on Dataflow
- `--project={project}`, the name of the GCP project
- `--streaming=true` to notify Dataflow that we're running a streaming application
- `--zone=europe-west2-a`, the zone where the Dataflow nodes (effectively [GCP Compute Engine](https://cloud.google.com/compute/) nodes) will be launched
- `--region=europe-west2`, the region where the Dataflow job will be launched
- `--gcpTempLocation=gs://location/`, the GCS bucket where temporary files necessary to run the job (e.g. JARs) will be stored

The list of all the options can be found at https://cloud.google.com/dataflow/pipelines/specifying-exec-params#setting-other-cloud-pipeline-options.

## Running

Beam Enrich comes as a ZIP archive or a Docker image, feel free to choose which fits your use case the most.

### ZIP archive

The ZIP archive is published on [our Bintray](https://bintray.com/snowplow/snowplow-generic/snowplow-beam-enrich).

If you'd like to create the archive from source, you can do so with
`sbt universal:packageBin`.

Once you have the archive unzipped, you can run it:

```bash
./bin/snowplow-beam-enrich \
  --runner=DataFlowRunner \
  --project=project-id \
  --streaming=true \
  --zone=europe-west2-a \
  --gcpTempLocation=gs://location/ \
  --job-name=beam-enrich \
  --raw=projects/project/subscriptions/raw-topic-subscription \
  --enriched=projects/project/topics/enriched-topic \
  --bad=projects/project/topics/bad-topic \
  --pii=projects/project/topics/pii-topic \ # OPTIONAL
  --resolver=iglu_resolver.json \
  --enrichments=enrichments/
```

You can also display a help message which will describe every Beam
Enrich-specific options:

```bash
./bin/snowplow-beam-enrich --runner=DataFlowRunner --help
```

### Docker image

The Docker image for Beam Enrich is also published on
[our Bintray](https://bintray.com/snowplow/registry/snowplow%3Asnowplow-beam-enrich).

You can build it yourself with `sbt docker:publishLocal`.

You can run a container with the following command:

```bash
docker run \
  -v $PWD/config:/snowplow/config snowplow-beam-enrich:0.1.0 \
  -e GOOGLE_APPLICATION_CREDENTIALS=/snowplow/config/credentials.json \ # if running outside GCP
  snowplow-beam-enrich:0.1.0 \
  --runner=DataFlowRunner \
  --project=project-id \
  --streaming=true \
  --zone=europe-west2-a \
  --gcpTempLocation=gs://location/ \
  --job-name=beam-enrich \
  --raw=projects/project/subscriptions/raw-topic-subscription \
  --enriched=projects/project/topics/enriched-topic \
  --bad=projects/project/topics/bad-topic \
  --pii=projects/project/topics/pii-topic \ # OPTIONAL
  --resolver=/snowplow/config/iglu_resolver.json \
  --enrichments=/snowplow/config/enrichments/
```

This assumes that you have a `config` folder containing your resolver
and your enrichments (as well as your GCP credentials if you're running
Beam Enrich outside of GCP) in the current directory.

## Tests and debugging

### Testing

The tests for this codebase can be run with `sbt test`.

### Debugging

You can run the job locally and experiment with its different parts using the
[SCIO REPL](https://github.com/spotify/scio/wiki/Scio-REPL) by running `sbt repl/run`.

[beam-enrich]: https://github.com/snowplow/snowplow/tree/master/3-enrich/beam-enrich
