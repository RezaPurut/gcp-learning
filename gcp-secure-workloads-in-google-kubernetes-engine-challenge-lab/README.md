## Task 0: Download the necessary files
1. Open cloud shell and run the following command.
```
gsutil -m cp gs://cloud-training/gsp335/* .
```

## Task 1: Setup Cluster
```
gcloud container clusters create kraken-cluster1  \
--node-locations=us-central1-c \
--num-nodes=2 \
--machine-type=n1-standard-4 \
--enable-network-policy \
--zone=us-central1-c
```
