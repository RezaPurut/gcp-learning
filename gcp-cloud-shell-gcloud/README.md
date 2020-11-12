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
## Using gcloud commands
```
gcloud -h
```
```
gcloud config --help
```
Use the Enter key or the Spacebar to scroll through the help content

**q** to exit
```
gcloud help config
```
```
gcloud config --help
```
These two commands above are same

View the list of configurations in environment:
```
gcloud config list
```
Check how other properties are set, see all properties:
```
gcloud config list --all
```
List components:
```
gcloud components list
```
## Auto-completion
Install beta components first
```
gcloud components install beta
```
Enter the interactive mode by;
```
gcloud beta interactive
```
Use **Tab** to *complete file path and resource arguments*. If a dropdown menu appears, use the **Tab** key to *move through the list*, and the **Space** bar to *select your choice*

To toggle this feature, try out the F2 toggle:

F2:help:STATE Toggles the active help section, ON when enabled, OFF when disabled
## SSH into your vm instance
```
gcloud compute ssh gcelab2 --zone $ZONE
```

