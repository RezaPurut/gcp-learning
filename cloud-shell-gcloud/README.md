# Getting Started with Cloud Shell & gcloud
## Setting environment variables
To ease us later. No need to type long name for zone, project id, etc.
```
export PROJECT_ID=<your_project_ID>
```
```
export ZONE=<your_zone>
```
Verify
```
echo $PROJECT_ID
echo $ZONE
```
## Create a virtual machine with gcloud
```
gcloud compute instances create gcelab2 --machine-type n1-standard-2 --zone $ZONE
```
The parameter that is not stated in the command above like machine type and image, will use default values.

Can use this command to see the default values
```
gcloud compute instances create --help
```
