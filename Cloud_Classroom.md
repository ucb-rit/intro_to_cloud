# Cloud Classroom

This tutorial will show you a few methods of deploying a workshop/teaching environment for students to use.

In these examples, I will use QB3's Python for Bioinformatics Bootcamp. This is a one week tutorial on Python for complete novices held twice yearly on Berkeley campus for around 50 students from the greater Bay Area. Every year getting the students to install Python and Jupyter before the bootcamp is a hassle, and the first class is always slow and difficult as students run into errors and require help running the tutorial notebooks.

By hosting these course materials in a reproducable environment in the cloud, students are able to begin working with no install, directly through their browser. This can flip the workshop model, allowing instructors to include installation instructions at the end of the workshop, after students have become familiar with the tools and learned their utility.

## MyBinder.org

MyBinder.org is a free cloud service that allows anybody to host a collection of Jupyter notebooks. Jupyter notebooks can include Markdown text interleaved with executable code, making them a great tool for teaching and sharing computational analysis. MyBinder.org allows you to publish your notebooks together with the software dependencies they are running on top of, so that students can run and interact with the notebooks through their browser as if it were a web-page.

Under the hood, MyBinder.org is a public implementation of BinderHub. You publish your notebooks in a GitHub repository, along with a few files listing the dependencies and requirements of your notebooks, and provide a link to your repository to MyBinder. MyBinder then builds a Docker container out of your repository, and gives you a link to access it. When someone follows the link MyBinder runs the Docker container, which runs Jupyter Notebook and configures the notebook to be accessible via web protocols, and redirects the browser to this new server. What looks like any other web-page to the user is this notebook running within Docker on the MyBinder server.

Here's the [full documentation on MyBinder](https://mybinder.readthedocs.io/en/latest/). Everything we do here is covered under the ["Preparing a Repository for Binder" section](https://mybinder.readthedocs.io/en/latest/using.html#preparing-a-repository-for-binder).

### 1. Make your course content.

On your local machine, write up your workshop/tutorial/class as Jupyter notebooks. Make sure to note which software and packages you are utilizing, and keep track of any sample data you will include.

Here I will just extract my Python_Bootcamp archive:

```bash
tar -xvzf Python_Bootcamp.tar.gz
cd Python_Bootcamp

```

### 2. Create a GitHub repository.

On GitHub.com, create a new repository. Make sure it is Public.

My GitHub handle is "channsoden" and in this example I named my repository "Python_Bootcamp".

Then, within your workshop directory:
```bash
echo "# Python Bootcamp" >> README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin git@github.com:channsoden/Python_Bootcamp.git
git push -u origin master
```

Now add all your course files to the repo:
```bash
git add ./*
git commit -m "upload course files"
git push origin master
```

### 3. Write your dependency files.

This version of the Python bootcamp was written in Python 2, so we need to make sure Python 2 is installed by specifying it in a "runtime.txt" file:

```bash

echo "python-2.7" > runtime.txt

```

We also teach a few non-standard Python modules in the workshop. If these can be installed with PIP (a Python package manager), then you can simply list them in "requirements.txt":

```bash
echo "numpy\nmatplotlib\npandas\nscipy" > requirements.txt

```

And we use the software package BLAST+, which is available through the Ubuntu package manager, APT:

```bash

echo "ncbi-blast+" > apt.txt

```

Lastly, we can install any custom software, as well as further customize our environment, by writing a "postBuild" script. This script is run after the other installs and you can put whatever you need in it. Here, I create a ProgramFiles directory and download BLAST into it, since we actually make the students install BLAST manually for practice. My postBuild script looks like this:

```bash
#!/bin/bash
mkdir ProgramFiles
cd ProgramFiles
wget http://ftp.ncbi.nlm.nih.gov/blast/executables/blast+/LATEST/ncbi-blast-2.7.1+-x64-linux.tar.gz

```

Now just to add these config files to the repo:

```bash

git add runtime.txt requirements.txt apt.txt postBuild
git commit -m "create configuration files"
git push origin master

```

### 4. Send it to MyBinder.org

Simply navigate to [MyBinder.org](https://mybinder.org), copy the URL of your GitHub Repo (https://github.com/channsoden/Python_Bootcamp for me), and paste it into the top field.

Then copy the URL provided by MyBinder (https://mybinder.org/v2/gh/channsoden/Python_Bootcamp/master for me), and keep it somewhere. Pasting it into the README.md file in the repository is  good place. You can also copy and paste the LaunchBinder badge if you want.

You can now follow the provided link to checkout your handiwork. The first time you launch the binder, or after making changes to the repo, MyBinder will spend some time building the Docker image. After the first launch subsequent launches should be a fair bit faster, as only a new instance need to be created.

## What's cool?

This is very easy. All it requires is a GitHub account. The installation process is also fairly simple for packages that are available through Ubuntu, PIP (Python), Conda, or MRAN (R).

Your notebooks are completely interactive. Anyone can run the code in your notebook, make changes to it, and generally muck about. Since it's running on the cloud they don't have any hardware or software requirements - just an internet connection and browser. Furthermore, since it's instanced, each user has their own environment and can't break anyobody else's instance.

## What's not cool?

Because this service is totally free it is pretty limited. You only have a few hundred MB of disk space to work with, a few GB of memory, and a single CPU. Moreover, the server will time you out after 10 minutes of inactivity, and there is a user limit of 100-200 simultaneous users. You can't run any serious computation with MyBinder, nor could you launch something that goes viral.

If a Ubuntu base doesn't work for you, or you have very specialized needs, the environment can be limitting as well. Although, compared to a multi-user server you have a surprising amount of control.
