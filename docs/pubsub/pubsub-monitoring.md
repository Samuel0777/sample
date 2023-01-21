# Pub/Sub Monitoring using Notification Channel
## Overview:


Pub/Sub integration to Monitoring Alert Notification Channel setup lets you create automated and programmatic workflows in response to alerts. Pub/Sub is a publish/subscribe queue that lets you build loosely coupled applications. Using Pub/Sub as your notification channel makes it easy to integrate with third-party providers for alerting and incident response workflows, you can use the Pub/Sub as a notification channel through the API and the Google Cloud Console. This is still in Beta phase.


### Dependencies:

* PubSub Topic created
* PubSub Subscription created
* Permission to create monitoring alert

Google Doc to create Alert for Notification Channel setup [Link](https://cloud.google.com/monitoring/support/notification-options#pubsub)

### Terraform Procedure:

Here is the [link](https://github.ford.com/fordpubsub/tfm-pubsub/tree/ceciliatest/monitoring) to terraform example module.

```sh
terraform init
terraform validate
terraform plan
terraform apply
```

* Now you are ready to consume your resource

### Disclaimer:

If you want to delete your resources please USE below command

```hcl
terraform destroy
```

   


