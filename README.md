# Creating A Docker Image

## Chose a base image

It's best to choose a base image that has official support and which strikes a balance between having most of the dependencies already installed and finding an image that is small in size.

Miniconda, for example, is a great choice for Python code that has dependencies that may themselves have further dependencies on external libraries (e.g., R libraries). It's smaller than the full anaconda images (3/17th the size in the following example) and requires the user to manually install the direct dependencies while managing all transitive dependencies for you. 

## Write a Dockerfile to Customize Your Image

The following example is for Python 2.7 code.

```
FROM continuumio/miniconda2:latest

RUN apt-get update && apt-get install -y --no-install-recommends git libnss-sss && rm -rf /var/lib/apt/lists/* \
 && conda install numpy scipy rpy2=2.8.6. \
 && git clone {URL of your repo}
 
WORKDIR /{directory name where the code you want to execute is located}
CMD ["/bin/bash"]
```


```FROM continuumio/miniconda2:latest```

`FROM`: Initializes a new build stage and sets the Base Image

This grabs the latest version of the miniconda2 Docker image. The Alpine variant is smaller but uses libc instead of glibc and a different package manager than the lab this image was designed for is familiar with, so a version that is Debian based was chosen. 

```RUN apt-get update && apt-get install -y --no-install-recommends git libnss-sss && rm -rf /var/lib/apt/lists/* \```

`RUN`: Will execute any commands in a new layer

```apt-get update``` uses the APT package manager to update the list of repositories available. ```apt-get install```  installs git and libnss-ss. When complete the ```rm -rf``` command deletes ```/var/lib/apt/lists/*``` to save considerable space. It's a cache of the repositories available to install from and can be fairly large in some scenarios. ```\```  is used to indicates that the RUN statement continues on the next line.

```&& conda install numpy scipy rpy2=2.8.6. \```

conda installs the latest version of numpy and scipy.  rpy2 requires a version compatible with 2.7 (the latest version isn't compatible) thus a version had to be specified.

```&& git clone {insert URL of git repository with your code here}```

There is more than one way to put code into an image. Using git allows for the codebase to change, and all one has to do is rebuild the image. If the code changes frequently requiring the image to be rebuilt, consider putting this on a separate RUN line to create a separate layer that to reduce build times via caching.

``` WORKDIR /{directory of the code you wish to execute}```

```WORKDIR:``` Sets the working directory for any RUN, CMD, ENTRYPOINT, COPY, and ADD instructions that follow it in the Dockerfile

```CMD ["/bin/bash"]```

`CMD`: Provides a default for an executing container. There can only be one CMD instruction in a Dockerfile. One would probably change this to execute your code with the arguments you desire. Bash was only used as a placeholder here.
