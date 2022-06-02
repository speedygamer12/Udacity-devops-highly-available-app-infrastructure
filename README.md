## infrastructure For Highly Availaible App

Contains 4 base Instances in two private subnets in different Availaibilt zones.
LoadBalncer DSN: http://serve-webap-r7kyrynrrzv3-1532804460.us-east-1.elb.amazonaws.com/

The infrastructure template was split into:
* s3Policy - contains template for creating an s3 bucket and the instance Profile roles and policy as well as outputs.
* network: Contains the template for network for the Infrastructure and outputs.
* servers: Contains the template for servers built and some network infrastructure and outputs.