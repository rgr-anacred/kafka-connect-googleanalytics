# kafka-connect-googleanalytics

## Current TODO's

* Crawl multiple views in one connector
* Allow for setting one field as a key
* Add an option to define all dimension values as key of the message
* Allow for partitioning based on one column
* How should we handle offsets? Maybe just use the current timestamp?
* Integrate the real time API as well (currently only using the reporting API)
* Figure out how avro schemas can use "_" while letting kafka topics use "-" in subject/topic names

## Testing

Well, automatically testing a library that requires two hard-to-mock dependencies (Kafka and Google Analytics) is a bit... difficult. If you want to test, follow those steps:

0. If you don't already have, create a Google Analytics service account, give it at least READ access to the Google Analytics View you want to extract data from, and download the JSON file containing the keys/secrets. 
1. in `src/main/resources`, copy the `test-conf.properties.template` file and remove the `.template` extensions, then fill out the required key/values (look at the downloaded JSON for that). 
2. Download the confluent kafka distribution and extract it into a sibling folder of this one (or adjust the path to confluent in the makefile).
3. Run `mvn test` to run some (pretty basic) unit tests. Those tests will make no assumptions about the data in your property - it's more of a smoke test that looks for obvious crashes and prints out some results so you can manually check if that's what you expected.
4. Start kafka and schema registry by running `make run-kafka`.
5. Run `make test-run` to start to send some data to kafka. In another terminal, run `make run-[avro-]consumer` to listen to the topic

# Adaptation for AlphaCred

## GA

Helper:
* [Reporting API V4 - sample](https://developers.google.com/analytics/devguides/reporting/core/v4/quickstart/service-java)
* [Reporting API - OAuth2](https://developers.google.com/analytics/devguides/reporting/core/v4/authorization)
* [Google API - OAuth2 (installed application)](https://developers.google.com/api-client-library/java/google-api-java-client/oauth2#installed_applications)



## Confluent

### Installation


Confluent on GKE (Google Kubernetes Engine): 
* [Confluent - doc helm](https://docs.confluent.io/current/installation/installing_cp/cp-helm-charts/docs/index.html)
* [GitHub - Helm Chart for Confluent](https://github.com/confluentinc/cp-helm-charts/blob/master/charts/cp-kafka/README.md)
* [Medium - Install on Google EKS](https://medium.com/google-cloud/installing-helm-in-google-kubernetes-engine-7f07f43c536e)
* [Medium - Install on Amazon EKS](https://medium.com/devopslinks/install-confluent-kafka-platform-onto-amazon-eks-using-helm-chart-33c36a3b112)
* For understanding:
  - [Medium - GCP Compute Stack explained](https://medium.com/google-cloud/gcp-the-google-cloud-platform-compute-stack-explained-c4ebdccd299b)
  - [Kubernetes Doc - Concepts](https://kubernetes.io/docs/concepts/)

### GA Connector

Helper:
* [Confluent Connector - Dev Guide](https://docs.confluent.io/current/connect/devguide.html)

For a java app, config:
```
ssl.endpoint.identification.algorithm=https
sasl.mechanism=PLAIN
request.timeout.ms=20000
bootstrap.servers=pkc-l915e.europe-west1.gcp.confluent.cloud:9092
retry.backoff.ms=500
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="<CLUSTER_API_KEY>" password="<CLUSTER_API_SECRET>";
security.protocol=SASL_SSL
```

API KEY & SECRET
IT2NTPAEOWLRT47E
N24WxwooVRRrf77gY8qmL1dCBTPh6SlKTPmqn3EF5jegJ0e6M46B5MHoxJZRX47U


### NIFI


Further discussion
https://discussion.confluent.io/t/how-to-connect-nifi-consume-publish-to-ccloud-kafka/846/8