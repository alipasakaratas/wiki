[**HOME**](Home) » [**SNOWPLOW SETUP GUIDE**](Setting-up-Snowplow) » [Step 3: Setting up Enrich](Setting-up-enrich) » [**Step 3.1: setting up EmrEtlRunner**](Setting-up-EmrEtlRunner) » Setting up end-to-end encryption

1. [Overview](#overview)
2. [Pre-requisites](#pre-reqs)
3. [Configuring EmrEtlRunner](#configure)
4. [Next steps](#next-steps)

<a name="overview"/>

## 1. Overview

It is possible to setup end-to-end encryption for EmrEtlRunner. For reference, you can check out
the dedicated EMR guide: https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-data-encryption-options.html.

<a name="pre-reqs">

## 2. Pre-requisites

### 2.1 Encrypting S3 buckets

For at rest encryption on S3, the buckets with which EmrEtlRunner will interact need to have SSE-S3
encryption enabled.

For more information, check out the dedicated AWS guide: https://docs.aws.amazon.com/AmazonS3/latest/dev/serv-side-encryption.html.

Keep in mind that turning this setting on is not retroactive. It effectively means that if you want
to have only encrypted data in your bucket you will need to go through the existing data and copy it
in place.

Also, if you're using the Clojure Collector, SSE-S3 encryption needs to be set up at the bucket
level.

### 2.2 Setting up an EMR security configuration

Through an EMR security configuraton, you can specify at the EMR level for which parts of your job
you want encryption to be enforced, the possibilities are:

- At rest on S3
- At rest on local disks
- In-transit

### 2.2.1 At rest encryption in S3

Once setup, S3 encrypts data as it writes it to disk.

By default, even without encryption setup, data is encrypted while in transit from EMR to S3
(e.g. for s3-dist-cp steps).

### 2.2.2 At rest encryption on local disks

When running the Snowplow pipeline in EMR, an HDFS is setup on the different nodes of your cluster.
Enabling encryption for the disks local to those nodes will have the following effects:

- HDFS RPC, e.g. between name node and data node, uses SASL
- HDFS block transfers (e.g. replication) are encrypted using AES 256
- Attached EBS volumes are encrypted using [LUKS](https://guardianproject.info/code/luks/)

When enabling this option, please keep the following drawbacks in mind:

- EBS root volumes are not encrypted, you need to use a custom AMI for that: https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-custom-ami.html
- KMS key usage is subject to pricing: https://aws.amazon.com/kms/pricing/
- It has a performance impact (e.g. when writing your enriched data to HDFS)

To set this type of encryption up you will need to create an appropriate KMS key, refer to the
AWS guide for more information:
https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html.

It is important to note that the role used in `aws:emr:jobflow_role` in the EmrEtlRunner
configuration needs to have the `kms:GenerateDataKey` policy.

### 2.2.3 In-transit encryption (Spark and MapReduce)

When running the Spark jobs of the Snowplow pipeline (enrich and shred) or consolidating s3-dist-cp
steps (e.g. using `--groupBy` or `--targetSize`), data is shuffled around the different nodes in
your EMR cluster. Enabling encryption for those data movements will have the following effects:

- MapReduce shuffles use TLS
- RPC and data transfers in Spark are encrypted using AES 256 if emr >= 5.9.0, otherwise RPC is
encrypted using SASL
- SSL is enabled for all things HTTP in Spark (e.g. history server and UI)

Be aware that this type of encryption also has a performance impact as data needs to be encrypted
when sent over the network (e.g. when running deduplication in the Shred job).

To set up this type of encryption, you will need to create certificates according to the guidelines
specified at https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-encryption-enable.html#emr-encryption-pem-certificate.

Note that, for this type of encryption to work, you will need to be in a VPC and the domain name
specified in the certificates needs to be `*.ec2.internal` if in us-east-1 or
`*.region.compute.internal` otherwise.


For more information, on all those types of encryption, you can refer to the dedicated guide:
https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-create-security-configuration.html.

<a name="configure">

## 3 Configuring EmrEtlRunner

To leverage the security configuration you created, you will need to specify it in the EmrEtlRunner
configuration at: `aws:emr:security_configuration`.

Additionally, you will need to tell EmrEtlRunner that it will have to interact with encrypted
buckets through: `aws:s3:buckets:encrypted: true`.

