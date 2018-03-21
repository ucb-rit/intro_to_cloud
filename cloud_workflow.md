# Reproducable Cloud Workflow

This tutorial will show you how to bundle your analysis scripts together with its dependencies into a container that can be more easily run on another machine. This lets you 1) create portable analysis environments that can be run on cloud VMs, and 2) share the entire workflow with colleagues who can run the analysis on their local machine. For researchers and scientists, this has huge value in the ability to create reproducable workflows.

In this example, I am preparing a computational tool for publication, along with my application analysis. I have already published the code [in a public GitHub repository](https://github.com/channsoden/BRAG), along with some documentation. I would have loved to simply write up my sample analysis as a Jupyter Notebook and then host the whole shebang on MyBinder.org, as was done in the "Cloud Workshop" tutorial. Unfortunately, I ran into two obstacles. First, my sample data consists of 19 Genomes (228 MB compressed), which is too large to host on GitHub. Second, I create some very large figures (I am looking at many genomes, after all) that require nearly 4 GB of memory to produce, which is over the limit for MyBinder.

I decided to host my large-ish data set on the Open Science Framework (OSF), and simply download and extract the data as the first step of my analysis pipeline.

Then, instead of creating a 1-click interactive environment with MyBinder, I decided to package up my workflow into a Docker image and publish the image in a public registry. This requires someone who wants to reproduce my analysis to know how to use Docker, but as we will see this isn't too hard.

## Publishing Data on OSF

The [Open Sciene Framework](https://osf.io) is a grant-funded platform for publishing and sharing scientific projects, from data, to analysis, to preprints and articles. For my purposes it has two key features 1) it's free and provides unlimited storage of files under 5 GB, and 2) it creates persistent URLs for resources and provides DOIs for projects, both of which enable citation and reliable access.

After creating an OSF account, I made a new project for my tool. Within the project, in the Files section, I select "OSF Storage" then click the "Upload" button that appears. I use the GUI to select the .tar.gz archive of my genomes. Now I can click "genomes.tar.gz", and see I have links to Download and Share the file. [Here is the share link.](https://mfr.osf.io/render?url=https://osf.io/vmkc8/?action=download%26mode=render) If I want to, I can also gcreate a DOI for the project and include it in my manuscript.

## Containerizing my Workflow with Docker

### Installing Docker

Installing Docker isn't so bad - but I'm very lazy. To avoid having to install Docker, I'm going to just upload my workflow and configuration files to GitHub, then build my analysis image using the Google Cloud Shell, which already has Docker installed.

For when you're ready to install Docker yourself, the guide is here:  
https://docs.docker.com/install/

You almost certainly want the "Docker CE x86_64" version for you operating system, unless you know what kind of wizardry you're attempting.

**Note:** You will need sudo privelige on any machine you use Docker on. This is why you can't use Docker on many shared systems (e.g Savio).

### Learning to Use Docker

I will show you the general workflow of Docker here, and how I use it to acheive my goals, but I won't dwell on the details. When you're ready to learn Docker yourself, you can start with the official docs. They are written for software types rather than academic researchers, so, for example, the first app they have you build is a few leagues above "Hello World", but if you just follow along with the steps it's actually fairly approachable.
https://docs.docker.com/get-started/

One thing they leave out that was helpful for me in the beginning is running your Docker containers with an interactive terminal.
```bash
docker run -it <image> bash
```
If you didn't specify a command to run at start in your Dockerfile, this will run the container and drop you into it so that you can poke around under the hood.

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

# Run sample_data/sample_analysis.py when the container launches
#CMD ["bash", "./sample_data/sample_analysis.sh"]
```

### Finding a Parent Runtime

The only strange part of my Dockerfile is the first statement, "FROM python:3". This statement actually does most of the work by letting my image inherit all the properties of a parent image. Since I mostly do Python, I chose to use the officially support Python:3 image as a base. This image includes Ubuntu Linux with Python 3 and many of the other basic tools, so I don't need to worry about configuring them. Everything I add is just frosting on top of this mostly-complete operating system.



### Building & Testing the Image




From the repo directory:

docker build -t popgen .


### Getting my Results Out of the Container

### Pushing my Image to a Public Registry

To push docker images to the public Docker registry, you will need a Docker account.

## Running my Analysis on Jetstream

