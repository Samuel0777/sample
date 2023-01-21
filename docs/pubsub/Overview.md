# Google Cloud PubSub

## What is GCP PubSub ?

Pub/Sub is an asynchronous and scalable messaging service that decouples services producing messages from services processing those messages. Pub/Sub is used for streaming analytics and data integration pipelines to ingest and distribute data. It's equally effective as a messaging-oriented middleware for service integration or as a queue to parallelize tasks.

Pub/Sub enables you to create systems of event producers and consumers, called publishers and subscribers. Publishers communicate with subscribers asynchronously by broadcasting events, rather than by synchronous remote procedure calls (RPCs).

## How to provision ?

You can provision topic and subscription by any of the below method

- gcloud cli
- terraform 

Terraform module with example can be found here - https://github.ford.com/gcp/tfm-pubsub


## How to provide permission ?

You can request for pub/sub service through FCP portal. Below are the Ford custom role needed for publishing and subscribing from pubsub topic

- ford.cloud.pubsub.subscriber - Subscribe the message from a topic

- ford.cloud.pubsub.publisher - Publish message to a topic

- ford.cloud.pubsub.operator - When you enable the service you will get a default viewer role

