# Running Virtual Machines in GCP 

The Google Cloud Platform (GCP) offers scalable, high-performance Virtual Machines (VMs) with a range of predefined and custom machine types. GCP has support for both *Linux* and *Windows*.

The Google Compute Engine is the platform Google offers to run and manage VM instances. The Compute Engine includes a command-line tool that enables you to manage your instances locally.  

### 1. Google Compute command-line tool 

[Install gcloud compute tool here](https://cloud.google.com/compute/docs/gcloud-compute/)


### 2. Create an instance 

**Create an instance with 4 vCPUs and 6 GB memory:**
```
gcloud compute instances create my-vm --custom-cpu 4 --custom-memory 6
```

### 3. VM instance vs container 

VM instances deploy on the host operating system. This can create runtime conflicts from the dependencies and libraries during deployment.

Containers are isolated from the host operating system applications and libraries, containing their own application software and dependencies.   

### 4. Deploy container 

**Deploy a container on a new VM instance:**
```
gcloud beta compute instances create-with-container my-vm \
--container-image NAME-OF-DOCKER-IMAGE
```    

### 5. Preemptible VMs

GCP has **Preemptible VMs** for affordable, short-lived compute instances. Preemptible VM instances can run up to 24-hours.   

#### Preemptible GPUs

In January 2018 GCP announced that GPUs can be used in Preemptible VMs for 50% cheaper than GPUs used in on-demand VM instances. 

Preemptible VMs can use NVIDIA K80 and NVIDIA P100 GPUs for $0.22 and $0.73 per GPU hour, respectively. As a reference point, first-time users on GCP get **$300 in free compute credit**. 

### 6. Cloud Shell 

A free Linux terminal that you can access in your web browser. Cloud Shell can be used to manage your GCP resources or to quickly run Linux commands. 

[See the Cloud Shell example here](GCP_Cloud_Shell.md) 

### 7. TensorFlow for Machine Learning 

[GCP tutorial for TensorFlow across VMs](https://cloud.google.com/solutions/running-distributed-tensorflow-on-compute-engine)

### Tutorials and resources  

[Quickstart Linux VM instructions can be found here.](https://cloud.google.com/compute/docs/quickstart-linux)

