# Reproducible Cloud Workflow

This tutorial will show you how to bundle your analysis scripts together with its dependencies into a container that can be more easily run on another machine. This lets you 1) create portable analysis environments that can be run on cloud VMs, and 2) share the entire workflow with colleagues who can run the analysis on their local machine. For researchers and scientists, this has huge value in the ability to create reproducible workflows.

In this example, I am preparing a computational tool for publication, along with my application analysis. I have already published the code [in a public GitHub repository](https://github.com/channsoden/BRAG), along with some documentation. I would have loved to simply write up my sample analysis as a Jupyter Notebook and then host the whole shebang on MyBinder.org, as was done in the "Cloud Classroom" tutorial. Unfortunately, I ran into two obstacles. First, my sample data consists of 19 Genomes (228 MB compressed), which is too large to host on GitHub. Second, I create some very large figures (I am looking at many genomes, after all) that require around 4 GB of memory to produce, which is over the limit for MyBinder.

I decided to host my large-ish data set on the Open Science Framework (OSF), and simply download and extract the data as the first step of my analysis pipeline.

Then, instead of creating a 1-click interactive environment with MyBinder, I decided to package up my workflow into a Docker image and publish the image in a public registry. This requires someone who wants to reproduce my analysis to know how to use Docker, but as we will see this isn't too hard.

## Publishing Data on OSF

The [Open Sciene Framework](https://osf.io) is a grant-funded platform for publishing and sharing scientific projects, from data, to analysis, to preprints and articles. Dr. Alex Paxton, a postdoc and BIDS fellow here at Berkeley, has recently used it as a record and data repository for a recent publication. You can check out [her project here](https://osf.io/x9ay6/), which links to her GitHub for code, her data, and her paper.

For my purposes OSF has two key features 1) it's free and provides unlimited storage of files under 5 GB, and 2) it creates persistent URLs for resources and provides DOIs for projects, both of which enable citation and reliable access.

After creating an OSF account, I made a new project for my tool. Within the project, in the Files section, I select "OSF Storage" then click the "Upload" button that appears. I use the GUI to select the .tar.gz archive of my genomes. Now I can click "genomes.tar.gz", and see I have links to Download and Share the file. The share link tries do display the file in the browser, which is useless for this compressed archive, so [Here is the download link.](https://osf.io/vmkc8/download) If I want to, I can also gcreate a DOI for the project and include it in my manuscript.

## Containerizing my Workflow with Docker

### Installing Docker

Installing Docker isn't so bad - but I'm very lazy. To avoid having to install Docker, I'm going to just upload my workflow and configuration files to GitHub, then build my analysis image using the Google Cloud Shell, which already has Docker installed.

For when you're ready to install Docker yourself, the guide is here:  
https://docs.docker.com/install/

You almost certainly want the "Docker CE x86_64" version for you operating system, unless you know what kind of wizardry you're attempting.

**Note:** You will need sudo privelige on any machine you use Docker on. This is why you can't use Docker on many shared systems (e.g Savio).

### Learning to Use Docker

I will show you the general workflow of Docker here and how I use it to acheive my goals, but I won't dwell on the details. When you're ready to learn Docker yourself, you can start with the official docs. They are written for software types rather than academic researchers, so, for example, the first app they have you build is a few leagues above "Hello World", but if you just follow along with the steps it's actually fairly approachable.  
https://docs.docker.com/get-started/

One thing they leave out that was helpful for me in the beginning is running your Docker containers with an interactive terminal.
```bash
docker run -it <image> bash
```
This will run the image and drop you into the container so that you can poke around under the hood.

### Writing the Dockerfile

MyBinder.org uses Docker under the hood, so the configuration files you need for binderizing a GitHub repo are actually pretty standard Docker config files. I included a "requirements.txt" file listing all of my Python dependencies, just as I would for binderizing the repo. If you haven't read it yet, you can read up on how to do that in the Cloud_Classroom tutorial.

The only other file I need to create is the Dockerfile. This is a fairly simple script that instructs Docker on how to build my image. The entirety of my script is:

```bash
# Use an official Python runtime as a parent image
FROM python:3

# Create BRAG directory
RUN mkdir /home/BRAG

# Set the working directory
WORKDIR /home/BRAG

# Copy the current directory contents into the container working directory
ADD . /home/BRAG

# Install any Python dependencies specified in requirements.txt
RUN pip install --trusted-host pypi.python.org -r requirements.txt


# Set the working directory for the sample analysis
WORKDIR /home/BRAG/sample_data

# Create a directory for the results
RUN mkdir /home/BRAG/sample_data/results

# Run sample_data/sample_analysis.py when the container launches
CMD exec bash sample_analysis.sh 1> results/sample_analysis.out 2> results/sample_analysis.err
```

### Finding a Parent Runtime

The only strange part of my Dockerfile is the first statement, "FROM python:3". This statement actually does most of the work by letting my image inherit all the properties of a parent image. Since I mostly do Python, I chose to use the officially support Python:3 image as a base. This image includes Ubuntu Linux with Python 3 and many of the other basic tools, so I don't need to worry about configuring them. Everything I add is just frosting on top of this mostly-complete operating system.

There are multiple places where you can find pre-cooked base images to use, including GitHub. For example, Jupyter maintains a set of notebooks for [data science](https://github.com/jupyter/docker-stacks/tree/master/datascience-notebook), [R](https://github.com/jupyter/docker-stacks/tree/master/r-notebook), [machine learning using TensorFlow](https://github.com/jupyter/docker-stacks/tree/master/tensorflow-notebook), and other [common research needs](https://github.com/jupyter/docker-stacks). Their inheritance diagram shows how different Docker images inherit from each other using the "FROM" statement. Starting with a basic Ubuntu image, each image adds a bit more software and specialization to the environment.

![Image inheritance diagram](https://github.com/jupyter/docker-stacks/raw/master/internal/inherit-diagram.png)

These images are published in registries like Docker Hub. A registry is a collection of repositories, so the Jupyter images I just mentioned are stored in Jupyter's repository. Similar to how Git is a version control tool and GitHub is a public registry for Git repositories, Docker is a containerizing tool and Docker Hub is a public registry of Docker images. For whatever your needs, [Docker Hub](https://hub.docker.com/) is the place to start looking for a parent image.

I found my Python 3 parent image on the [official Python repository](https://hub.docker.com/_/python/) on Docker Hub. The top of the page is very confusing with a huge list of tags, but scroll down to "How to use this image" to see an easy sample Dockerfile.

### Building & Testing the Image

As I said above, because my actual image is under 5 GB and is already hosted as a GitHub repo, I am just going to use the Google Cloud Shell to build my Docker image. To launch the shell, go to the [Google Cloud Platform Console](https://console.cloud.google.com) and log in with your Gmail or Bmail account. Then just click the **>_** icon in the top right (and "Launch Shell" if it's your first time). The shell will then pop up in the bottom of your browser tab.

First, I clone my repo into my home directory and cd into it:

```bash
git clone https://github.com/channsoden/BRAG.git
cd BRAG
```

Then from the repo directory:
```bash
docker build -t brag .
```

If I were building this on my local machine or something with a bit more hardware I could test the image here. But because BRAG uses more memory than your typical app, I'm going to push the image straight to the [Docker Cloud](https://cloud.docker.com/) registry and run it on another VM.

### Pushing my Image to a Public Registry

To push docker images to the public Docker registry you will need a Docker account. You can make one by following the link above. While there, I also made a public repository to publish my image too called "brag".

I log in to my Docker account on the cloud shell.

```bash
docker login
```

Then I tag the image with my username, the name of the repository I want to push it to, and a descriptive version tag.

```bash
docker tag brag channsoden/brag:cloudworkshop
```

I can check what I did with:

```bash
docker image ls
```

When I'm ready I upload the image.

```bash
docker push channsoden/brag:cloudworkshop
```

## Running my Analysis on Jetstream

XSEDE Jetstream is NSF's cloud platform. Relative to the commercial providers, it is relatively new and doesn't offer nearly as much in the way of resources. However, it is much simpler to learn and is entirely free for academic researchers through a granting process. Berkeley affiliates can get access often within 24 hours by contacting the Campus Champion (email brc@berkeley.edu), who can also lend assistance in writing a grant proposal. Lastly, Jetstream's focus is to provide for interactive analysis, and it's numerous GUI interfaces can be more approachable.

For those of you without Jetstream allocations, you can recreate these steps by installing Docker on your local machine and running it there.

I navigated to the [Jetstream Cloud Console](https://use.jetstream-cloud.org/) and created a new project (Cloud Workshop), and within that project create a new VM instance. I recommend using one of the featured images to start with, as they tend to be more reliable. To run my new image, I searched for "docker", and of the two featured images that are tagged with "docker", I selected the Centos 7 image. I then gave the instance a helpful name ("BRAG_box"), and set the instance size to "m1.medium" because I'll need the 16 GB of memory to run my analysis.

Once the VM is online, I can open a web shell and start weilding it immediately. Since Docker is already installed on this VM, I can just tell it to run my Docker image directly from my public repo. The only tricky thing to remember is that, while on the Google Cloud Shell my user was already configured with Docker privileges, that is not the case by default on the Jetstream VM. The result is that I need to run every docker command as sudo.

```bash
sudo docker run channsoden/brag:cloudworkshop
```

And that's it! We just went through all that work to acheive this: Any machine, with any operating system, so long as it has Docker installed can run my tool and sample analysis with a single command.

### Getting my Results Out of the Container

After running my container I need to pull my results out. For portability reasons you can't configure your images to simply utilize the host file system. You need to either designate a directory to mount into the container when you run it or pull out the results after the fact. I wrote my script to dump all the results in a single directory so that I can just copy that directory out when it's finished.

Your Docker images are like templates. If you know some computer science, the image is analagous to a class, while containers are instances of the class. When you run an image you create a container that is persistent and unique. To find the containers I have created I use:

```bash
sudo docker container ls -a
```

The last container I ran comes up on top, and it has both a unique ID string, and a unique two word human readable name. So, for example, with a container named "sleepy_jepsen" I can copy out my results directory to the current directory with:

```bash
sudo docker cp sleepy_jepsen:/home/BRAG/sample_data/results .
```

## Future Possibilities

In this tutorial I showed you how you can publish your entire research project so that others can reproduce it. Others will be able to download your data from OSF, view your source code on GitHub, and recreate your exact analysis with your Docker image.

The tools you have learned here, though, also enable you to do much more with the cloud.

For example, in my field of genomics data often comes in huge batches (several to 10s of TB) of sequencing data all at once. Many of the first steps in a genomics analysis are to do some quality control filtering on this data, then condense it down to several GB of relevant data (e.g. read counts, genomes, or summaries of mutations). These first steps often consume astonishing amounts of memory as they involve building sophisticated indexes for all-against-all type comparisons. 

For a small startup or other independent entity doing genomics work, this can mean occasional extreme computational needs, followed by long periods of analyzing the results. Containerizing your workflow can facilitate these kinds of burst computing applications.

Just as I created one VM and ran my docker analysis on it with a few clicks and commands, I could create a second and run another analysis there. When I get a batch of genome sequencing data from 200 samples all at once, I could even boot up 200 VMs on a commercial provider and have each one analyze a different sample.

For launching this many machines, though, it would be nice to do it in an automated way. That's why cloud providers create APIs for their services. An Application Programming Interface (API) is a documented set of tools that allow you interact with a service through a software or code interface, rather than a human interface. This allows you to automate tasks like "spinning up" VMs and pulling out the results.

A quick warning, though: When you attach a cloud service to your credit card, and then use it's APIs to automate it's usage, you are automating spending money. A dumb mistake could end up landing you with a fat bill for a whole lot of useless service.


[**Next: Running Virtual Machines in Google Cloud Platform**](GCP_VMs.md)
