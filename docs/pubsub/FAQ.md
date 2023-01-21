# PubSub FAQs

## Overview

The purpose of this document is to answer commonly asked questions about using PubSub at Ford. If you have a question not discussed or require clarification please join the:

[Kafka PubSub Teams Space](https://www.webexteams.ford.com/space?r=bvgq)

## Questions & Answers

### 1. How to setup Push Subscriptions

> Refer to tfm github repo
https://github.ford.com/gcp/tfm-pubsub#disclaimer-to-all-pubsub-push-subscription-users

### 2. Publicly accessible topic

> Refer to the following link https://github.ford.com/gcp/tfm-pubsub#publicly-accessible-pubsub-topic 

### 3. What regions are available currently in GCP PubSub?

> 1. US Central 1 and US East 4 
>
> 2. How to provision for multi region? (Please check with Kafka/PubSub team) 

### 4. How do I provision my project in 2.0 

> Customers need to put in request via FCP portal.  Please refer to the link https://www.cloudportal.ford.com/multi-cloud to create project 

### 5. Dead Letter Topic terraform examples

> Please refer to github link https://github.ford.com/gcp/tfm-pubsub/tree/master/examples 
for both example on push and pull 

### 6. We are working on creating a GCP pubsub topic and subscription in our QA project using terraform, but we are facing the below error:

```in terraform-apply stage [terraform-cli] Error: Error creating Topic: googleapi: Error 403: Cloud Pub/Sub API has not been used in project ID before or it is disabled. Enable it by visiting https://console.developers.google.com/apis/api/pubsub.googleapis.com/overview?project=projectnumber then retry. If you enabled this API recently, wait a few minutes for the action to propagate to our systems and retry.```

> APIs need to be enabled through FCP 

### 7. Does a pipeline service account need the Ford Cloud PubSub Service write role in order to create PubSub resources using that module 

> That service account needs provisioner role

### 8. How to Integrate PubSub with Dataflow

> Please visit the link below to find more information on this:
https://cloud.google.com/architecture/deploying-production-ready-log-exports-to-splunk-using-dataflow 


### 9. We are facing permission denied error while publishing message to pubsub from cloud run. Do we need to provide roles in iam.serviceAccountTokenCreator access to Service account 

> The service account (in this case Cloud run SA) Should have publisher role to the topic and you can either use terraform or bind via FCP. 

### 10. Can you provide us the terraform link for PubSub 

> https://github.ford.com/gcp/tfm-pubsub 


### 11. We are trying to publish an event from spring boot project to google pubsub service. Can someone help me implement this? or If there is any material or doc to follow can you please share it with me?

> We do not have client specific documentation, you can start reading through spring documentation for pubsub publisher/subscriber  - https://spring.io/guides/gs/messaging-gcp-pubsub/
> 
> We can help you on how you can authenticate or autorize to your pubsub topic from clients

### 12. How to listen pubsub from project to project

> You will access the pub sub subscription to access topic in another project will be similar to accessing topic in your own project. You need to request source project to provide access to service account which you will use to provision your subscription
	>
	>1.	Project A that owns the topic must provide “Subscriber Role” to the service account  of Project B. Access is provided at resource level using terraform, in this case to a particular topic, in order to achieve Granular Access.
  	>
	>2.	Project B also need to have “Provisioner Role” in their own project in case they want to create the subscription.
	>
> Please refer the documentation for more details - https://github.ford.com/fordpubsub/tfm-pubsub/tree/master/examples/pubsub_granular_Control 
> 
### 13. The messages in pubsub are not consuming fast for the service deployed in cloud run. Is there any way to scale up pubsub or any another configurations to consume the messages quickly

> One way to achieve higher/faster consumption of messages is to send them out in batches as it would help to increase throughput. Also if your subscriber is running on cloud run then go for a push subscription instead of pull subscription

### 14. We had our kafka topic created for 1.0 gcp projects. Now we have created 2.0 projects. Would like to know the process to onboard our new projects on kafka platform so that we can create topic for new projects.

> Eventhough you provide your gcp project during onboarding in the ticket, there is no link between gcp project and kafka. So you can use the same topic even from your gcp project 2.0 without any issue.

### 15. Using Pubsub if we do originalMessage.nack() incase of exception, will that message forwarded to deadletterqueue without any retries

> No our understanding even if you nack() the message it will retry for mentioned number if times before moving the message to DLQ.
>
>If the client holds the message beyond the agreed deadline without acking, or if the client sends a nack (negative acknowledgement) request, the message will be redelivered (potentially to a different subscriber on the same subscription).

### 16. We are trying to invoke a cloud run API on a scheduled basis for our business use case with the help of Cloud Scheduler via PubSub Push Subscription invoking the Cloud Run endpoint URL

> Can you try this example module how to push message from pubsub to cloud run - https://github.ford.com/gcp/tfm-pubsub/tree/master/examples/pubsub_push_cloudrun
>
>Can you please check if the service account you are using have the invoke permission in cloud run.
Please refer this documentation you need to create push subscription with service account and make sure you provide that service account the cloud run invoke permission.
>
>https://cloud.google.com/run/docs/triggering/pubsub-push#create-push-subscription

### 17.	What permissions are needed to create a Bigquery subscription through the web interface? 

>Grant the BigQuery Data Editor (roles/bigquery.dataEditor) role and the BigQuery Metadata Viewer (roles/bigquery.metadataViewer) role to the Pub/Sub service account.(also known as Pubsub service agent)
More info can be found in the below links. 
https://cloud.google.com/pubsub/docs/bigquery
https://cloud.google.com/pubsub/docs/create-subscription#subscription

### 18.	What level of access is required to pull messages from GCP Pub-Sub
>
>Pls ensure that the SA has subscriber role for that topic if its hosted in other project. source project should provide subscriber role to your SA project

### 19.	We are trying to implement google pub/sub While deploying in GKE getting GOOGLE_APPLICATION_CREDENTIALS error , any suggestion?

>Caused by: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'topicAdminClient' defined in class path resource [com/google/cloud/spring/autoconfigure/pubsub/GcpPubSubAutoConfiguration.class]: Bean instantiation via factory method failed; nested exception is org.springframework.beans.BeanInstantiationException: Failed to instantiate [com.google.cloud.pubsub.v1.TopicAdminClient]: Factory method 'topicAdminClient' threw exception; nested exception is com.google.cloud.spring.pubsub.core.PubSubException: An error occurred while creating TopicAdminClient.; nested exception is java.io.IOException: The Application Default Credentials are not available. They are available if running in Google Compute Engine. Otherwise, the environment variable GOOGLE_APPLICATION_CREDENTIALS must be defined pointing to a file defining the credentials. See https://developers.google.com/accounts/docs/application-default-credentials for more information.

>Please refer the document on how to authenticate to any GCP service when using GKE. https://cloud.google.com/docs/authentication/provide-credentials-adc#containerized

>You may have to use the Workload Identity  - https://cloud.google.com/kubernetes-engine/docs/how-to/workload-identity

### 20.	Permission Denied trying to create a Dataflow from the template "Pub/Sub to Avro Files on Cloud Storage"?

>You are missing on  permissions what dataflow requires to create a dataflow-pipeline, Please reachout to dan deewale : ddewael5@ford.com

### 21.	Trying to implement dataflow scheduler using Cloud Scheduler - pubsub - cloud run. It looks like we can not put message in cloud scheduler pubsub topic as the data variable is not used. Is there a work around? the cloud scheduler integration with pubsub doesn't have data field enabled in the repo. As of now it sends data as "test" hard coded. Can this be exposed for the users to assign needed data values?
>As this feature what you are asking is part of scheduler module - https://github.ford.com/gcp/tfm-scheduler. This is managed by different team you need to work with cloud scheduler team to get this change updated in their module
>
>Additional info: 
Terraform module that will guide how to publish to pub/sub topic using cloud scheduler - https://github.ford.com/gcp/tfm-pubsub/blob/master/examples/cloud-scheduler/scheduler.tf.
>How to push the message to cloud run using push subscription - https://github.ford.com/gcp/tfm-pubsub/tree/master/examples/pubsub_push_cloudrun

### 22.	Trying to create a log sink at the folder level and have that log sink publish to a pubsub topic. Originally we were getting a topic_org_policy_denied. Since then we have updated the writer identity to have the correct permissions. We are now getting a permission denied. Any help would be much appreciated.

>you need logging.viewer and logging.privateLogViewer permissions added to the account

### 23.	Where can I get more help on Terraform? 

>For any Terraform related issue please reach out to GCP DevOps Dojo and Community Support Form -  https://www.webexteams.ford.com/space?r=m1gv

>Refer the link for documentation - https://docs.gcp.ford.com/docs/support/local-dev/

### 24.	Where can I find the pubsub service account that was created when I selected the service for my project?

>Go to IAM in GCP project, then Check the check box on the right side of the IAM UI where it says Google provided roles and grants.
>Then Scroll to very bottom and you will find a pubsub service agent with below naming convention.
Service-your_project_id@
Select one which contains pubsub after "@".


### 25.	getting this error while creating a topic with pull subscriptions. This is only happening for the topic which has dead letter flag enabled. I am on VPN and authenticated. also other topic(without dead letter reference) and its pull subscriptions are created in the same terraform apply

>│ Error: Error creating Subscription: googleapi: Error 403: Request is prohibited by organization's policy. vpcServiceControlsUniqueIdentifier: mt7Cjn3GV4mnxFVr4Mb4BAqhlr_L0uqRC8mRTOe77P9jQq8fyTQUYw
│ Details:
│ [
│   {
│     "@type": "type.googleapis.com/google.rpc.PreconditionFailure",
│     "violations": [
│       {
│         "description": "mt7Cjn3GV4mnxFVr4Mb4BAqhlr_L0uqRC8mRTOe77P9jQq8fyTQUYw",
│         "type": "VPC_SERVICE_CONTROLS"
│       }
│     ]
│   },
│   {
│     "@type": "type.googleapis.com/google.rpc.ErrorInfo",
│     "domain": "googleapis.com",
│     "metadata": {
│       "consumer": "projects/232007124988",
│       "service": "pubsub.googleapis.com",
│       "uid": "mt7Cjn3GV4mnxFVr4Mb4BAqhlr_L0uqRC8mRTOe77P9jQq8fyTQUYw"
│     },
│     "reason": "SECURITY_POLICY_VIOLATED"
│   }
│ ]
│ 
│   with module.base.module.pubsub-vadr-release-topic.google_pubsub_subscription.pull_subscriptions["au-na"],
│   on .terraform/modules/base.pubsub-vadr-release-topic/main.tf line 98, in resource "google_pubsub_subscription" "pull_subscriptions":
│   98: resource "google_pubsub_subscription" "pull_subscriptions" {

>need to provide the deadletter topic name in the below format

>`projects/<Project-id>/topics/<topic-name>`

>Please contact terraform SME for additional support. 


### 26.	Unable to decode the published messages correctly
>PubSub treats the payload as a byte[]. The encoding and decoding should be up to you. If this is java, make sure you are defining the right character set in the byte[] to String logic. Perhaps python has something similar. 
