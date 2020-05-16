# Cloud Engineering: Challenge Lab
## Task 1: Create development VPC manually
Create a VPC called `griffin-dev-vpc` with the following subnets only:

`griffin-dev-wp`
* IP address block: 192.168.16.0/20

`griffin-dev-mgmt`
* IP address block: 192.168.32.0/20

1. Create the VPC
```
gcloud compute networks create griffin-dev-vpc --subnet-mode=custom
```
2. Create the subnets
```
gcloud compute networks subnets create griffin-dev-wp \
--network griffin-dev-vpc --region=us-east1 --range=192.168.16.0/20
```
```
gcloud compute networks subnets create griffin-dev-mgmt \
--network griffin-dev-vpc --region=us-east1 --range=192.168.32.0/20
```
