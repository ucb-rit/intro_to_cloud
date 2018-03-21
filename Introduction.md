# Introduction to the Cloud 

This workshop will cover a general, basic introduction to using the Cloud.

## Why the Cloud?

Cloud resources typically provide a high-level of control over compute workflow. Through the use of virtual machines (VM), cloud resources offer the ability to create virtual images of specific compute environments, then run those environments on scalable virtually-rented machines.

Savio and other traditional HPC resources (*e.g. XSEDE's Comet*) often act as the go-to compute resources for the campus community. However, these resources do not offer sufficient capabilities to cover all research needs. The examples we will cover in this workshop demonstrate some of the capabilities found in the cloud that may not be available in traditional HPC clusters. 

## Example Use Cases

We will aim the workshop at practical use of the cloud for research with four examples: 

**1. Deploying a workshop / teaching environment for student use**

**2. Creating reproducible workflows** 

**3. Using a Cloud API for research** 

**4. Deploying a web app** 

## Tools and Terminology 

Best practices in the cloud involve several steps / tools to create reproducible, accessible research.  

**Version Control** *(e.g. Github)*

Version control is essential for best coding practices and reproducible research. Github is a great platform for Git version control and is completely free. The examples in this workshop take advantage of using Github by itself and with other tools for work in the cloud. 

**Virtual Machines** 

Virtual Machines (VMs) act like independent computers with their own operating system (OS) and software installed. Users can use a VM through a virtual environment, including web browsers or local software.

**Virtual Images** 

Virtual images are snapshots of an OS and software on it. These images are pre-built environments that can be loaded into VMs. 

**Containers** *(e.g. Docker)*

**Docker** is a popular platform for containers across cloud providers.

Containers are similar to the virtual images above but tend to go a few steps further with encapsulating an environment. Rather than installing the system software you use on a VM or the existing software on a cluster, all application code and software dependencies are bundled together into a container. A Docker image of the container can be installed and run on any system with Docker installed. The image contains a reproducible compute environment, including code and all related dependencies.    

**Notebooks** *(e.g. Jupyter)* 

Notebooks are great tools for creating interactive and reproducible workflows. 

**MyBinder** 

A free cloud service that allows users to host Jupyter Notebooks. 

## Cloud Providers 

#### XSEDE Jetstream (NSF-funded)

XSEDE Jetstream is NSF's publicly funded cloud service. It is one of the many resources offered through the NSF-funded XSEDE program. XSEDE is an ecosystem of compute resources offered at the national level.

Jetstream offers interactive Virtual Machines that users can access through a web browser. There are virtual images   

**Access to Jetstream can start with a 24-hour turnaround.** 

#### Commercial Providers 

There are several commercial cloud providers. These providers offer limited resources through *Always Free Tier* of use and free compute credits across all of their resources for first-time users. 

- Amazon Web Services (AWS) 

- Microsoft Azure 

- Google Cloud Platform (GCP) 

- DigitalOcean 
