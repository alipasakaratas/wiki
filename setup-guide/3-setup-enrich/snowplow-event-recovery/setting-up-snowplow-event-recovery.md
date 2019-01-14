<a name="top" />

[HOME](Home) » [SNOWPLOW SETUP GUIDE](Setting-up-Snowplow) » [Step 3: Setting up Enrich](Setting-up-enrich) » Setting up Event Recovery

[[https://d3i6fms1cm1j0i.cloudfront.net/github-wiki/images/3-enrich.png]]

## Overview of Snowplow Event Recovery

Refer to the [[Snowplow Event Recovery]] document.
You can find the code [on GitHub][snowplow-event-recovery].

## Runtimes

### Spark for AWS real-time

The Spark job reads bad rows from an S3 location and stores the recovered payloads in another S3
location.

#### Building

To build the fat jar, run: `sbt "project spark" assembly`.

#### Running

Using the JAR directly (which is hosted at `s3://snowplow-hosted-assets/3-enrich/snowplow-event-recovery/`):

```bash
spark-submit \
  --class com.snowplowanalytcs.snowplow.event.recovery.Main \
  --master master-url \
  --deploy-mode deploy-mode \
  snowplow-event-recovery-spark-0.1.0.jar
  --input s3://bad-rows-location/
  --output s3://recovered-collector-payloads-location/
  --config base64-encoded-configuration
```

Or through an EMR step:

```bash
aws emr add-steps --cluster-id j-XXXXXXXX --steps \
  Name=snowplow-event-recovery,\
  Type=CUSTOM_JAR,\
  Jar=s3://snowplow-hosted-assets/3-enrich/snowplow-event-recovery/snowplow-event-recovery-spark-0.1.0.jar,\
  MainClass=com.snowplowanalytics.snowplow.event.recovery.Main,\
  Args=[--input,s3://bad-rows-location/,--output,s3://recovered-collector-payloads-location/,--config,base64-encoded-configuration],\
  ActionOnFailure=CONTINUE
```

### Beam for GCP real-time

The Beam job reads data from a GCS location specified through a pattern and stores the recovered
payloads in a PubSub topic.

#### Building

To build the zip archive, run: `sbt "project beam" universal:packageBin`.

To build the docker image, run: `sbt "project beam" docker:publishLocal`.

#### Running

Using the zip archive (which can be downloaded from Bintray [here](https://bintray.com/snowplow/snowplow-generic/snowplow-event-recovery/)):

```bash
./bin/snowplow-event-recovery-beam \
  --runner=DataFlowRunner \
  --project=project-id \
  --zone=europe-west2-a \
  --gcpTempLocation=gs://location/ \
  --inputDirectory=gs://bad-rows-location/* \
  --outputTopic=projects/project/topics/topic \
  --config=base64-encoded-configuration
```

Using a Docker container (for which the image is available in our registry on Bintray [here](https://bintray.com/snowplow/registry/snowplow%3Asnowplow-event-recovery-beam)):

```bash
docker run \
  -v $PWD/config:/snowplow/config \ # if running outside GCP
  -e GOOGLE_APPLICATION_CREDENTIALS=/snowplow/config/credentials.json \ # if running outside GCP
  snowplow-docker-registry.bintray.io/snowplow/snowplow-event-recovery:0.1.0 \
  --runner=DataFlowRunner \
  --project=project-id \
  --zone=europe-west2-a \
  --gcpTempLocation=gs://location/ \
  --inputDirectory=gs://bad-rows-location/* \
  --outputTopic=projects/project/topics/topic \
  --config=base64-encoded-configuration
```

## Testing

You'll need to have cloned this repository to run those tests and downloaded
[SBT](https://www.scala-sbt.org/).

### A complete recovery

You can test a complete recovery, starting from bad rows to getting the data enriched by:

- Modifying the [`bad_rows.json`](https://github.com/snowplow-incubator/snowplow-event-recovery/blob/master/core/src/test/resources/bad_rows.json) file which should contain
examples of bad rows you want to recover
- Adding your recovery scenarios to
[`recovery_scenarios.json`](https://github.com/snowplow-incubator/snowplow-event-recovery/blob/master/core/src/test/resources/recovery_scenarios.json)
- Fill out the payloads you're expecting to generate after the recovery is run in
[`expected_payloads.json`](https://github.com/snowplow-incubator/snowplow-event-recovery/blob/master/core/src/test/resources/expected_payloads.json). Here you have the choice
of specifying a payload containing a querystring or a payload.
- If your recovery is relying on specific Iglu repositories additionally to Iglu central, you'll
need to specify those repositories in [`resolver.json`](https://github.com/snowplow-incubator/snowplow-event-recovery/blob/master/core/src/test/resources/resolver.json)
- If your recovery is relying on specific enrichments, you'll need to add them to
[`enrichments.json`](https://github.com/snowplow-incubator/snowplow-event-recovery/blob/master/core/src/test/resources/enrichments.json)

Once this is all done, you can run `sbt "project core" "testOnly *IntegrationSpec"`.
What this process will do is:

- Run the recovery on the bad rows contained in `bad_rows.json` according to the configuration in
`recovery_scenarios.json`
- Check that the recovered payloads outputted by the recovery conform to the contents of the
expected payloads in `expected_payloads.json`
- Check that these recovered payloads pass enrichments, optionally leveraging the additional Iglu
repositories and enrichments

### A custom recovery scenario

If you've written an additional recovery scenario you'll need to add the corresponding unit tests to
[`RecoverScenarioSpec.scala`](https://github.com/snowplow-incubator/snowplow-event-recovery/blob/master/core/src/test/scala/com.snowplowanalytics.snowplow.event.recovery/RecoveryScenarioSpec.scala)
and then run `sbt test`.

[snowplow-event-recovery]: https://github.com/snowplow-incubator/snowplow-event-recovery/
