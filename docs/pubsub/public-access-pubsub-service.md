# Publicly Accessible Pub/Sub Topic 

If your project has a business need for the publicly accessible Pub/Sub topic and need a VPC Service Control (Ingress/Egress) Policy, a signed Detailed Risk Assessment must first be completed by the project team.  In order to initiate the Assessment, a request can be filed HERE (more filing detail below).

## For Detailed Risk Assessment request:

*   [Click Here](https://www.at.ford.com/en/homepage.html)
*   Click `‘IT Help’`
*   Click `‘Submit a Ticket’`
*   Search for `“Risk Assessment”`
*   In the line…

![Sample_ticketSGR.png](./img/Sample_ticketSGR.png)
 
*   Click `“Request Now”`

*   Make these initial choices…

![Sample_ticketRequest details.png](./img/Sample_ticketRequest%20details.png)

*   And fill in the rest specific to your application.  Include that the purpose of the analysis is to measure the risks of Publicly Accessible Pub/Sub topics that are located within our Ford.com VPC Perimeter.

*   Click `‘Submit Request’`

##  Step3: 

The VPC Service Controls rule necessary to resolve the error is an Ingress rule, which will be applied by VPC Service Control team. 

##  Example Ingress Rule:

Sample VPCSC Request: [REQ000004671596](https://ford-smartit.onbmc.com/smartit/app/#/workorder/IDGJRO5LAO6ROAR2UW22R1V6E7YMUF)

### Place the below code snippet within both Dry Run and Enforce VPC Perimeters.

### Method needed for Subscription:

            {method = "Subscriber.Pull"},
            {method = "Subscriber.Seek"},
            {method = "Subscriber.Acknowledge"},
            {method = "Subscriber.StreamingPull"},
            {method = "Subscriber.GetSubscription"},
            {method = "Subscriber.ModifyAckDeadline"},
            {method = "SubscriberService.Pull"},
            {method = "SubscriberService.Acknowledge"},

```sh
{
        from = {
          identity_type = "IDENTITY_TYPE_UNSPECIFIED"
          identities = ["serviceAccount:vault-SA-Name@prj-example.iam.gserviceaccount.com"]
          sources    = { access_level = "*" }
        }
        to = {
          resources = ["projects/123456789"]
          operations = [{
            service_name = "pubsub.googleapis.com"
            #Specifying Specific PubSub Methods that an External Customer can use
            method_selectors = [
            {method = "Subscriber.Pull"},
            {method = "Subscriber.Seek"},
            {method = "Subscriber.Acknowledge"},
            {method = "Subscriber.StreamingPull"},
            {method = "Subscriber.GetSubscription"},
            {method = "Subscriber.ModifyAckDeadline"},
            {method = "SubscriberService.Pull"},
            {method = "SubscriberService.Acknowledge"},
            {method = "Publisher.GetTopic"}
            ]
          }]
        }
      },
```

### Methods to be added to Ingress for a Publisher:

            {method = "Publisher.GetTopic"},
            {method = "Publisher.ListTopics"},     
            {method = "PublisherService.ListTopics"},
            {method = "PublisherService.Publish"},
            {method = "PublisherService.PublishBatch"},
            {method = "Publisher.Publish"}


```sh
{
        from = {
          identity_type = "IDENTITY_TYPE_UNSPECIFIED"
          identities = ["serviceAccount:vault-SA-Name@prj-example.iam.gserviceaccount.com"]
          sources    = { access_level = "*" }
        }
        to = {
          resources = ["projects/123456789"]
          operations = [{
            service_name = "pubsub.googleapis.com"
            #Specifying Specific PubSub Methods that an External Customer can use
            method_selectors = [
            {method = "Publisher.ListTopics"},
            {method = "PublisherService.ListTopics"},
            {method = "PublisherService.Publish"},
            {method = "PublisherService.PublishBatch"},
            {method = "Publisher.Publish"},
            {method = "Publisher.GetTopic"}
            ]
          }]
        }
      },
```