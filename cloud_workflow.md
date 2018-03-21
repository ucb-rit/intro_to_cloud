# Reproducable Cloud Workflow

This tutorial will show you how to bundle your analysis scripts together with its dependencies into a container that can be more easily run on another machine. This lets you 1) create portable analysis environments that can be run on cloud VMs, and 2) share the entire workflow with colleagues who can run the analysis on their local machine. For researchers and scientists, this has huge value in the ability to create reproducable workflows.

In this example, I am preparing a computational tool for publication, along with my application analysis. I have already published the code [in a public GitHub repository](https://github.com/channsoden/BRAG), along with some documentation. I would have loved to simply write up my sample analysis as a Jupyter Notebook and then host the whole shebang on MyBinder.org, as was done in the "Cloud Workshop" tutorial. Unfortunately, I ran into two obstacles. First, my sample data consists of 19 Genomes (228 MB compressed), which is too large to host on GitHub. Second, I create some very large figures (I am looking at many genomes, after all) that require nearly 4 GB of memory to produce, which is over the limit for MyBinder.

I decided to host my large-ish data set on the Open Science Framework (OSF), and simply download and extract the data as the first step of my analysis pipeline.

Then, instead of creating a 1-click interactive environment with MyBinder, I decided to package up my workflow into a Docker image and publish the image in a public registry. This requires someone who wants to reproduce my analysis to know how to use Docker, but as we will see, this isn't too hard.

## Publishing Data on OSF

## Containerizing my Workflow with Docker

### Installing Docker
https://docs.docker.com/install/
You almost certainly want the "Docker CE x86_64" version for you operating system.

You will need sudo privelige on any machine you use Docker on. This is why you can't use Docker on many shared systems (e.g Savio).

### Learning to Use Docker
https://docs.docker.com/get-started/

### Writing the Docker File

### Finding a Parent Runtime

Since I mostly do Python, I used the officially support Python:3 image as a base.

### Building & Testing the Image

### Getting my Results Out of the Container

### Pushing my Image to a Public Registry

To push docker images to the public Docker registry, you will need a Docker account.

## Running my Analysis on Jetstream

