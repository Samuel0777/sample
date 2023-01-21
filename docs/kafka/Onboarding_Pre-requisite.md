# Onboarding Pre-requisite

Below are the pre-requisites for kafka onboarding.

> **Note** It is mandotory to  have the security groups created for admin, publisher and subscriber with correct naming standards before onboarding. In case of incorrect naming standards for FIM group or topic name, the onboarding will fail.

## Authentication and Authorization options
There are two authorization option are provided for customers.

#### Option 1: Authentication & Authorization - Generic Control (all topics prefixed with an app id)

> **Note**: Security group will have a wider permission for managing, publishing and consuming from topic. i.e access will be provided on prefix based i.e the security group will have access to all topic prefixed with an appid**

***Below are the naming standards for the Security Groups***

```text
kafka-AppID-env-adm <== Developer-Manage
kafka-AppID-env-pub <== Developer-Write
kafka-AppID-env-sub <== Developer-Read


For Example:
APPID - 53993
Environment - prod

So the security group has to be in the below format.

kafka-53993-prod-adm
kafka-53993-prod-pub
kafka-53993-prod-sub
```


#### Option 2: Authentication & Authorization - Fine Grained Control (individual topics)

> **Note: Fine grain access request where topic authorization is provided for a specific topic. This option control access at topic level so each topic will have a specific security groups that control publisher and consumers**

***Below are the naming standards for the Security Groups***

```text
kafka-AppID-env-adm <== Developer-Manage

For each topic name:
kafka-AppID-env-pub-topicname <== Developer-Write
kafka-AppID-env-sub-topicname <== Developer-Read

For Example:
APPID: 53993
Environment: prod
Topic Name: a53993-prod-testtopic

So the security group has to be in the below format.

kafka-53993-prod-adm
kafka-53993-prod-pub-testtopic
kafka-53993-prod-sub-testtopic
```
##### 1. Users may have different namings for preprod environment (pp,qa,stage, etc). The environment naming from the topic name and security group must be the same.
```text
If the topic name is "a53993-pp-testtopic", then the FIM groups namings must be 

kafka-53993-pp-adm
kafka-53993-pp-pub-testtopic
kafka-53993-pp-sub-testtopic
```
##### 2. The topic name format must be "*a(AppID)-(env)-(topicname)*". 
##### 3. The publisher and subscriber security groups must have topic name in it after -pub or -sub

```text
** env = environment the topic will be hosted i.e. dev, pp, prod etc.
** AppID = application id for your project application
** topicname = the topic in which members of the specific security group can read and write to the specific topic respectively.
```
