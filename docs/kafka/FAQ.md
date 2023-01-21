# Kafka FAQs

## Overview

The purpose of this document is to answer commonly asked questions about using Confluent Kafka at Ford. If you have a not question not discussed or require clarification please join the:

[Kafka PubSub Teams Space](https://www.webexteams.ford.com/space?r=bvgq)


## Questions
   * [1. Do we have to create consumer group manually, or does it require any permissions?](#1-do-we-have-to-create-consumer-group-manually-or-does-it-require-any-permissions)
  * [2. Our consumer app is throwing this error- `org.apache.kafka.common.errors.GroupAuthorizationException: Not authorized to access group: <our group id>`](#2-our-consumer-app-is-throwing-this-error--orgapachekafkacommonerrorsgroupauthorizationexception-not-authorized-to-access-group-our-group-id)
  * [3. Failure while consuming the data `Error: Authentication failed: Invalid username or password`](#3-failure-while-consuming-the-data-error-authentication-failed-invalid-username-or-password)
  * [5. Intermittent timeout error `org.apache.kafka.common.errors.TimeoutException: Timed out waiting to send the call. Call: fetchMetadata`](#5-intermittent-timeout-error-orgapachekafkacommonerrorstimeoutexception-timed-out-waiting-to-send-the-call-call-fetchmetadata)
  * [6. Unable to update the topic configuration like retention time](#6-unable-to-update-the-topic-configuration-like-retention-time)
  * [7. Will we be able to reduce the partition count?](#7-will-we-be-able-to-reduce-the-partition-count)
  * [8. How to increase/decrease the replication factor](#8-how-to-increasedecrease-the-replication-factor)
  * [9. Does Confluent Kafka encrypt data at rest?](#9-does-confluent-kafka-encrypt-data-at-rest)
  * [10. Is there a way to determine the cost incurred for the topics that we have created and the amount of data that is flowing through the topics?](#10-is-there-a-way-to-determine-the-cost-incurred-for-the-topics-that-we-have-created-and-the-amount-of-data-that-is-flowing-through-the-topics)
  * [11. Some app teams are unsure about who is obligated to create topic](#11-some-app-teams-are-unsure-about-who-is-obligated-to-create-topic)
  * [12. Providing better documentation for connecting to Kafka cluster, with dependencies that a user machine requires](#12-providing-better-documentation-for-connecting-to-kafka-cluster-with-dependencies-that-a-user-machine-requires)
  * [13. A link for tutorials on How to connect to Kafka cluster with CLI or Client Libraries](#13-a-link-for-tutorials-on-how-to-connect-to-kafka-cluster-with-cli-or-client-libraries)
  * [14. Is there any case sensitivity with CDS IDs?](#14-is-there-any-case-sensitivity-with-cds-ids)
  * [15. Are there specific naming conventions for topics and consumer groups?](#15-are-there-specific-naming-conventions-for-topics-and-consumer-groups)
  * [16. Should be using qa.iam.ford.com to create PP FIM groups and dev.iam.ford.com to create the dev FIM groups?](#16-should-be-using-qaiamfordcom-to-create-pp-fim-groups-and-deviamfordcom-to-create-the-dev-fim-groups)
  * [17. Consumer Application deployed on GCP cloud run while producer and consumer initialization getting following error:](#17-consumer-application-deployed-on-gcp-cloud-run-while-producer-and-consumer-initialization-getting-following-error)
  * [18. What is minimum requirements for my `properties` configuration to connect?](#18-what-is-minimum-requirements-for-my-properties-configuration-to-connect)
  * [19. How to Setup replication factor of 3?](#19-how-to-setup-replication-factor-of-3)
  * [20. I want to know more on Kafka permissions. Where can I find more information?](#20-i-want-to-know-more-on-kafka-permissions-where-can-i-find-more-information)
  * [21. Is there an existing pattern on what to do during a Kafka outage for producers and consumers?](#21-is-there-an-existing-pattern-on-what-to-do-during-a-kafka-outage-for-producers-and-consumers)
  * [22. Where can i download kafka client cli and do i need to install them?](#22-where-can-i-download-kafka-client-cli-and-do-i-need-to-install-them)
  * [23. Can i use both options of authentication and authorization for onboarding i.e onbaord few topic usign generic A&A and others using fine grained?](#23-can-i-use-both-options-of-authentication-and-authorization-for-onboarding)
  * [24. Cluster IPs for internal and external Kafka clusters](#24-Cluster-IPs-for-internal-and-external-Kafka-clusters)
  * [25. Should we be able to see published confluentKafka messages in the UI](#25-Should-we-be-able-to-see-published-confluent-Kafka-messages-in-the-UI)
  * [26. currently our topic is created on kafka.pp01.kafka.ford.com server. In which region it is. how can we create topics in other region](#26-currently-our-topic-is-created-on-kafka-pp01-kafka-ford-com-server-In-which-region-it-is-how-can-we-create-topics-in-other-region)
  * [27. Our team is exploring ways to connect kafka with bigquery. Do we have any sample project or best practices we can use to load data from Kafka to BigQuery](#27-Our-team-is-exploring-ways-to-connect-kafka-with-bigquery-Do-we-have-any-sample-project-or-best-practices-we-can-use-to-load-data-from-Kafka-to-BigQuery)
  * [28. I was experiencing this issue while running kafka](#28-I-was-experiencing-this-issue-while-running-kafka)
  * [29. For externally facing Kafka, do we need to submit a request per topic? Is there generic access available for external facing kafka](#29-For-externally-facing-Kafka-do-we-need-to-submit-a-request-per-topic)
  * [30. Is there any documentation to understand the kafka schema registry](#30-Is-there-any-documentation-to-understand-the-kafka-schema-registry)
  * [31. We are trying to implement retry mechanism. Would like to know the right way to implement it. kindly advice](#31-We-are-trying-to-implement-retry-mechanism-Would-like-to-know-the-right-way-to-implement-it-kindly-advice)       
  * [32. We have our Kafka on prem instance and have setup the relevant roles. We have created generic user with interactive login and added it to the FIM admin group, but when attempting to connect there we get an auth error rejecting the username or password. Does anyone know if it takes a while for the newly created generic user to sync to wherever Kafka is checking](#32-We-have-our-Kafka-on-prem-instance-and-have-setup-the-relevant-roles-We-have-created-generic-user-with-interactive-login-and-added-it-to-the-FIM-admin-group-but-when-attempting-to-connect-there-we-get-an-auth-error-rejecting-the-username-or-password-Does-anyone-know-if-it-takes-a-while-for-the-newly-created-generic-user-to-sync-to-wherever-Kafka-is-checking)  
  * [33. Is there a RabbitMQ connector installed in Kafka. If not when it will be available](#33-Is-there-a-RabbitMQ-connector-installed-in-Kafka-If-not-when-it-will-be-available) 


## Questions & Answers

### 1. Do we have to create consumer group manually, or does it require any permissions?

> Yes, there is no separate steps needed to create consumer group. When consumer application start reading the message using a consumer group those are logged in kafka. Please follow the naming standard as mentioned in the onboarding email whil you configure consumer group in your application

### 2. Our consumer app is throwing this error- `org.apache.kafka.common.errors.GroupAuthorizationException: Not authorized to access group: <our group id>`

> *Make sure consumer group follows the prefix rule and is the member of the particular sub group for that topic*

### 3. Failure while consuming the data `Error: Authentication failed: Invalid username or password`

> 1. All username accounts must be lower case, may need to restart application, make sure you are in the correct FIM group
>
> 2. Check if the user password expired and in that case you may need to be renew the password and update your clients

### 5. Intermittent timeout error `org.apache.kafka.common.errors.TimeoutException: Timed out waiting to send the call. Call: fetchMetadata`

> This timeout exception can happen when the the client is not able to get metadata information within `max.block.ms`, which is 1 minute by default. Configure your client configuration to set higher value for `max.block.ms` i.e. 120000

### 6. Unable to update the topic configuration like retention time

> Customers will have limited update permission for topic configuration like retention period Customers need to open ticket to Kafka team (Request ticket)

### 7. Will we be able to reduce the partition count?

> No, kafka archiecture will not allow to reduce the partition counts

### 8. How to increase/decrease the replication factor

> There are two way to do it:
>
> 1. If this was a test topic and there are no data application team can delete the topic and recreate with required replication factor.
>
> 2. Incase the topic has data and need to be retained, you need to contact kafka service team through IT connect ticket to update the replication factor

### 9. Does Confluent Kafka encrypt data at rest?

> Confluent kafka does not encrypt the data, however underlying disk offered by GCP storage class where the data us stored is auto encrypted by GCP

### 10. Is there a way to determine the cost incurred for the topics that we have created and the amount of data that is flowing through the topics?

> Cost is `$5/GB/Month`
>
> E.g. if the app Message Generation Rate 10GB per day, Replication Factor = 3, Retention Period =7; Then Cost is calulcated as:
> `10GB x 3 x 7 = 210GB x $5 = $1050/month.`

### 11. Some app teams are unsure about who is obligated to create topic

> Owner of the topic must create topic, whoever is producing to that topic

### 12. Providing better documentation for connecting to Kafka cluster, with dependencies that a user machine requires

> Please visit document in [GitHub](https://github.ford.com/fordkafka/confluent-kafka-documentation/blob/master/how_to_connect/how-to-connect-to-kafka.md)

### 13. A link for tutorials on How to connect to Kafka cluster with CLI or Client Libraries

> [Tutorials](https://developer.confluent.io/tutorials/)

### 14. Is there any case sensitivity with CDS IDs?

> Yes, CDS IDs used in app or CLI configurations must always be a lower case to avoid the publisher/consumer authorization failure

### 15. Are there specific naming conventions for topics and consumer groups?

> Yes, naming conventions for Topic and consumer group must be strictly followed for avoiding authorization issues for creation, producing, and consuming. You must follow the prefix provided in your onboarding e-mail.

#### 16. Should be using qa.iam.ford.com to create PP FIM groups and dev.iam.ford.com to create the dev FIM groups?

> `iam.ford.com` should be used when creating FIM Groups to onboard to Kafka.

### 17. Consumer Application deployed on GCP cloud run while producer and consumer initialization getting following error:

```bash
org.apache.kafka.common.errors.SslAuthenticationException: SSL handshake failed Caused by: javax.net.ssl.SSLHandshakeException: No subject alternative DNS name matching b1.pp01.kafka.ford.com found
```

> By default, cloud run kills all the background threads if there is no incoming traffic. Below configurations need to be added before application is deployed to keep background thread alive. This will avoid the SSL error:

```bash
min_instance_count = "1" 
memory_size        = "2148" 
cpu_count          = "2" 
cpu_throttling     = false
```

### 18. What is minimum requirements for my `properties` configuration to connect?

> For Windows:

```pwsh
bootstrap.servers=kafka.bootstrap-url:443 
sasl.mechanism=PLAIN  
security.protocol=SASL_SSL  
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="<CDSID>" password="<CDSID-PASSWORD>"; 
ssl.truststore.location=\\Users\\cdsid\\truststore.jks 
ssl.truststore.password=<truststore_password>
```

> For Mac:

```bash
bootstrap.servers=<kafka-bootstrap-url:443> 
sasl.mechanism=PLAIN 
security.protocol=SASL_SSL 
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \ 
  username="<cds_id>" \ 
  password="<your_password>";
```

### 19. How to Setup replication factor of 3?

```bash
kafka-topics.sh --bootstrap-server <kafka-bootstrap-server:443> \ 

--command-config=<config.properties> \ 

--topic <topic-name> \ 

--create --replication-factor 3 --partitions 3 
```

### 20. I want to know more on Kafka permissions. Where can I find more information?

> For RBAC predefined roles please have a look at this [documentation](https://docs.confluent.io/platform/current/security/rbac/rbac-predefined-roles.html#role-based-access-control-predefined-roles)

### 21. Is there an existing pattern on what to do during a Kafka outage for producers and consumers?

> For transient and temporary outages we like the producers and consumers re-try operations. For extended outages like regional failures we have multi-region Kafka, that require application design accordingly; please refer this [document](https://azureford.sharepoint.com/sites/CloudPlatform_Data_Service/Kafka/Kafka%20Docs/Service%20Documents/Kafka-PubSub%20Service%20v4-2022-Customer.pdf) `Slides 8,9,10` for details, if required we can please reach out in teams space or schedule a meeting with service team.

### 22. Where can i download kafka client cli and do i need to install them?

> Please download the latest kafka binary files from https://kafka.apache.org/downloads. There are not installation process, unzip the binary files and you the client can be used readly. However you need to have java installed on your machine as pre-requisite to run those kafka cli

### 23. Can i use both options of authentication and authorization for onboarding

> No application can using only either one of the option while onboarding. Application team can use generic authentication and authorization or Fine grained authentication and authorization. 

### 24. Cluster IPs for internal and external Kafka clusters

 | Environment | Region    | Kafka end Point        | Public Internet Enabled |
 | ----------- | --------- | -------------------------- | ---------- |
 | preprod     | central 1 | kafka.pp01.kafka.ford.com        | N          |
 | prod        | central 1 | kafka.pd01.kafka.ford.com        | N          |
 | prod        | central 1 | kafka.pd51.kafka.ford.com        | Y          |
 | preprod     | east4     | kafka.pp31.kafka.ford.com        | N          |
 | prod        | east4     | kafka.pd31.kafka.ford.com        | N          |
 | prod        | east4     | kafka.pd61.kafka.ford.com        | Y          |

### 25. Should we be able to see published confluent Kafka messages in the UI

> If you are part of consumer FIM group you will be able to see the message from UI. However we strongly recommend to use a CLI or basic java/python client to consume/read data from the topic.
>
>Here are some examples how to consume via CLI or client
>
>https://github.ford.com/fordkafka/confluent-kafka-documentation/blob/master/how_to_connect/kafka-cli-commnds.md

>https://github.ford.com/fordkafka/confluent-kafka-documentation/tree/master/examples

### 26. currently our topic is created on kafka.pp01.kafka.ford.com server. In which region it is. how can we create topics in other region 

>We have updated the confluent documentation with the endpoint and cluster - 
>https://github.ford.com/fordkafka/confluent-kafka-documentation/blob/master/README.md
>
>Each region has different endpoint, so you need to onboard on to another region and once completed you will be create topic same way as you are doing now.

### 27. Our team is exploring ways to connect kafka with bigquery. Do we have any sample project or best practices we can use to load data from Kafka to BigQuery

>currently connectors are not offered as we still have not completely tested the security and authorization model.
>
>There is no ready made service, you may have to built a simple stream application and write those data to big query using API

### 28. I was experiencing this issue while running kafka.
Plugin [id: 'gradle-boost', version: '4.0.0'] was not found in any of the following sources
- Gradle Core Plugins (not a core plugin, please see https://docs.gradle.org/7.4/userguide/standard_plugins.html for available core plugins)        
- Plugin Repositories (could not resolve plugin artifact 'gradle-boost:gradle-boost.gradle.plugin:4.0.0')
  Searched in the following repositories:
    Gradle Central Plugin Repository
>
>The error looks more like some plug in error while try to build the application. We can help with any Kafka configuration related query. We will not be able to provide much help or support on the application code.
>
>The sample shared in our document is also from another application team sample, can you reach out to them they might be able to help you on the error

### 29. For externally facing Kafka, do we need to submit a request per topic?
>
>For the public Internet facing cluster currently only authentication allowed in Fine grained, where authentication is per topic.
>
>Each topic will be bound a separate pair of security group (pub and sub), application team has to raise a new request for each topic with a pair of security group following the below naming
For each topic name:
>
>kafka-AppID-env-pub-topicname <== Developer-Write
>
>kafka-AppID-env-sub-topicname <== Developer-Read  

### 30. Is there any documentation to understand the kafka schema registry
>
>Please refer this URL
>https://docs.confluent.io/platform/current/schema-registry/index.html

### 31. We are trying to implement retry mechanism. Would like to know the right way to implement it. kindly advice
>
>In case your requirement is to enabling retry mechanism in consumer, please check the client configuraiton RetryListener provided by kafka spring framework 
>
>https://docs.spring.io/spring-kafka/reference/html/#retrying-deliveries
>
>As long as you are not using auto commit, and you handle the commit manually, consumer will be able to read the same message. There are no specific standard for retry
 > - As some case application team will move any invalid messages to dead letter queue and process to next message.
>
>  - Any transient or network error consumer will be able to poll the mesage when it read the same next time. 
>  
>  For Producer kafka client does by default perform retry based on the retry configured in producer client. (By default it will keep trying)

### 32. We have our Kafka on prem instance and have setup the relevant roles. We have created generic user with interactive login and added it to the FIM admin group, but when attempting to connect there we get an auth error rejecting the username or password. Does anyone know if it takes a while for the newly created generic user to sync to wherever Kafka is checking

>Yes. We have a backend job which need to run to get these users to our kafka. This may usually take 1-2 hours, i would suggest you to test after 2 hours

### 33. Is there a RabbitMQ connector installed in Kafka. If not when it will be available

>Currently there are no connectors offered at kafka for any services. Currently there is no date attached when we will be able enable this feature, as we are still working with vendor to understand how this connector can be provisioned and security around them so customer can provision their own connectors instead of relying on ITO team.

### 34. Do we have Kafka Free version available for testing? Ford Cloud Portal Free version is grayed out?

>There are no trial offering from confluent kafka, however there is apache kafka which is a open source kafka. You may need to install them locally for your learning or testing
 
### 35. We have on proxy user and do anyone knows how to give permission to that proxy user also for Topic. As I dont want to expose my credentials, that is the reason we have created the proxy user.

>If this is for kafka please add proxy user to your pub FIM group if they are producing and if they are a consuming add user to your sub FIM group for that topic. 
 
### 36. Is there any way we can Publish the Topic using Postman ? or We have to use either CLI/Control Center ?

>Kafka endpoint are not http end points, so you cannot publish to a bootstrap using postman. You may need to use CLI or a simple client Java/Python - https://github.ford.com/fordkafka/confluent-kafka-documentation/tree/master/examples
 
 ### 37. In order to publish topic from outside Ford Network, do we need to do any extra white listing ?  I see there are only two options to publish the topic, either through cli or Control Center. Can CLI work from outside Ford Network?

>We have a Public facing Kafka cluster, where u can onboard to produce/consume topic from outside ford. We don't have control centre option to publish topic from outside ford. 
>You may need to use CLI or a simple client Java/Python - https://github.ford.com/fordkafka/confluent-kafka-documentation/tree/master/examples
 
### 38. I have reviewed both examples from https://github.ford.com/fordkafka/confluent-kafka-documentation/blob/master/examples/java-springboot/SpringBoot-Sample.md The first - only one kafka cluster - and the second - multiple clusters - use different client implementations Spring Kafka and Spring Cloud Stream for Kafka Is there a recommendation which one to use? I guess with having only one cluster both would work? Does the first not work when there are multiple kafka clusters to be supported by the app?

>Both spring cloud stream and spring Kafka will work on any of our clusters. It all boils down to developer’s choice and preference whatever is best for your app.  So, I would encourage you to take a good look at what each framework and Kafka interaction entails with it. If you really do desire an extra cluster space to test you can always onboard to an alternate region cluster via fcp for multi cluster testing or if you’re using non fine grained control access to Kafka you may always create a separate topic to compare as well to compare different frameworks as well. If you are using fine grain create the extra topic and submit via fcp for the additional 2 pub and sub groups.

### 39. There an issue with my topic. The partitions are not getting online.

>You could produce and consume to the topic and you are seeing this issue as you topic may have replication.factor less than min.insync.replicas.
This is just a warning as you may face issue while producing if you have set the acks=all in your producer config. Make sure you have topic created with replication.factor  >= min.insync.replicas (i.e ideally replication factor as 3)

### 40. Is there anyway we can get a count of total kafka messages posted in a topic during a certain time period?

>We can't get count of messages/events produced to topic, but can be only measured after its consumed by consumer by tracking offset.

>One other way is to you keeo a note of offset value in each partition and them find the difference at the end of the testing.
