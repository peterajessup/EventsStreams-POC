# MM2 Migration
# TODO
1. Finish Readme...
2. Document further MM2 configurations


## Pre-requisites
- Source kafka cluster
- Target Kafka cluster

[`See MirrorMaker2 Schema Reference for full configuration details`](https://strimzi.io/docs/operators/0.40.0/configuring.html#type-KafkaMirrorMaker2-reference)
## Source Configuration
### Kafka User for Migration
Create a migration principal within the source cluster 
for a principal in the Source Cluster, for example `Migration-SA` with the following ACL's.
```yaml
# access to the topics that MirrorMaker will consume messages from
- operations:
    - DescribeConfigs
    - Read
resource:
    name: LH.
    patternType: prefix
    type: topic
# access to the consumer groups that Mirror Maker will sync
- operations:
    - Describe
resource:
    type: group
    name: consumer-
    patternType: prefix
# access to the consumer groups that Mirror Maker will sync
- operations:
    - Describe
resource:
    type: cluster
```
> Note that in this instance, we've given the principal Access to Read and DescribeConfigs only of the topics with the prefix `LH.`

## Target Configuration 
Neccesary steps to configure the MM2 Custom Resource
1. Declaritively create the topic for Migration within event streams. `KafkaTopic / eventstreams.ibm.com/v1beta2`. Topic properties should mirror those in source cluster.
2. Create a Kafka User with the appropriate ACL's to authenticate against the topic for migration. An example [MM2 KafkaUser](./mm2-user-ap4.yaml) is provided.
3. Create a secret containing the credentials associated with the Principal in the source cluster. This will be referenced in the source cluster configuration within the MM2 CR. For example, using SCRAM authenticaton:
```yaml
authentication:
  username: Migration-SA
  passwordSecret:
    password: password
    secretName: Migration-SA-Creds
  type: scram-sha-512
```
4. Similarly, create a secret containing the credentials for the Kafka User created in **(2).**
5. A secret containing the CA certficate of the source cluster, used to establish TLS and refrenced within the source cluster configuration within the MM2 Custom Resource:
```yaml
tls:
  trustedCertificates:
    - certificate: ca.crt
    secretName: source-cluster-ca
```
6. Similarly, a secret containing the target cluster CA. Referencing the secret created in **(5)** if the same CA cert is used.
7. Review the [mm2.yaml](./mm2.yaml) file, updating source and target cluster details as above.

## Running
Creating the MM2 Custom Resource will initiate the mirroring of topics, consumer groups and offsets to the target cluster.
This will create multiple topics containin confiugration andstate information. Thse are denoted:
```yaml
mm2-*
<ES-CLUSTER>.checkpoints.internal
```


