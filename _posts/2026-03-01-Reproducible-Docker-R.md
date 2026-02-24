---
title: Fully Computationally Reproducible R Workflows with Docker, pak and renv (2026 Edition)
layout: post
categories: blog
tags:[R, docker, rocker, reproducibility, science, coding, renv, statistics, pak]
published: false
---
Hey Friend, 

you guessed right: I'm not the first one to write about this topic. Others have done this before me. Many, others, in fact, like this one or [this one](https://raps-with-r.dev/repro_cont.html), [this one](https://huixinng.com/articles/intro-to-docker-for-reproducible-analyses-in-r), [this one](https://haines-lab.com/post/2022-01-23-automating-computational-reproducibility-with-r-using-renv-docker-and-github-actions/), [this one](https://rstudio.github.io/renv/articles/docker.html), [this one](https://medium.com/data-science/running-rstudio-inside-a-container-e9db5e809ff8), or, famously, [that one](https://eliocamp.github.io/codigo-r/en/2021/08/docker-renv/), which this post is kind of based on (thank you for laying the groundwork, Elio!). While being cool, all of them have one or more of the following issues: 

- Some are old/outdated
- Some are not made for already existing projects
- Some do not make it as easy as possible to reproduce the result

But listen, there is good news. 

**I WILL FIX ALL OF THIS (mostly)**

In this blog post, I will show you how you can: 

1. Start your analysis pipeline with computational reproducibility in mind - from scratch!
2. Translate your already existing analysis into a fully computationally reproducible product. 
3. Use Docker and podman, two of the most useful tools in the modern computing environment to make stuff computationally reproducible.
4. Circumvent pitfalls with all of the above. 
5. Make your product as user-friendly as possible. 

Big goals. But before that, a little bit of a disclaimer as to 1) who this blog is written for, 2) what it is we're doing and 3) why we are doing it. 

#### Disclaimer 
This blog is for people unfamiliar with docker, or people familiar with docker who never used rocker and tried to use it for reproducibility. This means, I will go a bit into operational details with docker and its prerequisites---something people familiar with the program might perceive as a bit too "goo goo gaga" for their taste. But this is fine. Just take what you need and leave the rest. 
The technique I outline will enable your product to be fully computationally reproducible. If you're in science, you might know the "data available upon request" meme. It's something authors write in their paper when they want to pretend to be open and inclusive and all that, while either having lost their data, made it up in the first place, or milked it in unethical ways. Of course, sometimes they are just plain lazy---which I suspect is the broad majority. Regardless: With computational reproducibility, we can at least see how the authors got to the results they report in the article based on their data and code. Note that computational reproducibility is not the same as methodological reproducibility, or even integrity. All this blog post will tell you is to make your analysis reproducible for others. Not your setup, not your survey, just the analysis. Reproducibility does not mean your result isn't based on fraud, incompetence or misused analysis. So don't take this as an excuse to manipulate your way into a publication while claiming moral superiority because your fraud is reproducible. That is not how it works. But for now, back to the topic at hand.

#### Five things We Need To Get Going

1. A bit of Linux knowledge (Linux users may skip this)

    "Why Linux when I use $GARBAGE_OS for my analyses?", you might ask. Well, I will not judge you for liking garbage. Hell, I've lived off of garbage for a long time. But docker basically runs on Linux. You might have heard about Docker for Windows, but that runs on the Linux subsystem for Windows (WSL), not Windows itself. So here's everything you need to know about Linux to use docker. Linux runs basically on the command line, and with docker, it's best to get used to that. While Docker desktop exists, I consider it enshittified UI trash. I personally find dealing with the command line clearer and, frankly, easier. The command line lives in the terminal, in Windows that's the cmd or powershell thing. You click on it and type in commands. 
    The commands you need to know with docker are quite simple actually. There's `cd`, short for change directory. It takes a path after it as an argument, which is the destination where you want to go and sets your working directory of the command line to that folder. You can input any valid folder path into cd, it will take you there.
    
    ~~~~bash
    #this changes the working directory of the command line to the mbess-project folder
    cd home/documents/r-projects/mbess-project
    ~~~~
    
    Another interesting command is [cp](https://www.geeksforgeeks.org/linux-unix/cp-command-linux-examples/). This literally means copy paste and takes two file paths as an argument: The origin and destination paths. Note that if the filename in the destination already exists, it is overwritten without asking. If there is no source file by that name, it is created without asking.  
    
    ~~~~bash
    #This copies the file "text.txt" from documents to downloads
    cp home/documents/text.txt home/downloads/text.txt
    ~~~~
    
    Then you might want to know about [ls](https://www.geeksforgeeks.org/linux-unix/ls-command-in-linux/). This command lists all files and folders in your current directory and gives you a broad overview what to get.
    
    ~~~~bash
    #gives all contents of the mbess-project folder
    cd home/documents/r-projects/mbess-project
    ls
    ~~~~
    
    You can view files in the command line with `cat`. Note that this always assumes the current working directory that is shown in the command line prompt. 
    
    ~~~~bash
    #this prints the file contents to the command line
    cat filename.r
    ~~~~
    
    *Installing Docker*
    
    You usually always need admin rights to install docker, this is true for any system if I remember correctly. This most likely means contacting your IT department if your IT is somewhat centralized and knows what it's doing, so keep this in mind. On Windows, the easiest way to install docker is through Docker Desktop (and then forget about docker desktop). On Mac you will need homebrew or something---you may google this, I am not a Mac user and do not intend to burden my brain with knowledge of that system.

    Once installed, you immediately (and trust me, this is important) need to disable root needs. Docker usually needs admin privileges (called "root") to run, and you really don't want that. Firstly, because your admin may not trust you enough to execute random containers (maybe containing malware) with admin privileges, and also because it's a big nuisance to always enter admin credentials for docker to work. Consult [this](https://docs.docker.com/engine/security/rootless/) and ask your favourite IT person (which is definitely not me) to help you if you need it. Don't be shy, they will be glad to see you want to learn things.

2. Docker/podman

    Docker and podman are container software. Containers are cool, because they are like virtual machines, just smaller and less intensive for your digital abacus. They can provide a backend running some operating system, and also provide pre-installed software that runs on that operating system. You can detach and attach them from your machine with one command. Isn't that handy?As an example, docker was used to deploy images for [*phishy mailbox*](https://github.com/Enterprize1/phishy-mailbox), a phishing research software Thorsten Thiel and I created. You plug it in and use it in the browser, no matter what your operating system behind the browser is. If you've ever been barred from using software because your system can't run it, think about how cool it is to not need to care about that.
    
    Docker is the more seasoned version of the two, and is somewhat proprietary, but free to use for non-profit things or private persons---it also (still) scales better with really big applications and clusters than podman. Podman is a free and open source container software, which is becoming more and more popular among the community. It is highly compatible with docker and basically does most of the same things. I will focus on docker in this post, because I personally haven't dabbled with podman much so far, and don't feel too confident talking about it. But eventually, it is my goal to implement this approach fully with podman as well.Docker has a few basic commands we need to know for our workflow:
    
    - pull: Gets us docker images from the web onto our machine.
    - builder prune: clears building cache.
    - build: builds our custom image.  
    - rmi: removes built or pulled images. 
    - login/logout: used to make our images public.
    - push: this command pushes your docker image to a container registry like docker hub, github (ghcr) or codeberg(forgejo).

3. Rocker

    [Rocker](https://rocker-project.org/) is a family of pre-configured docker images. The base version comes with r installed, but there are also versions with Rstudio (server) and the tidyverse pre-installed. This is highly useful, because it gives us a head-start in our endeavour. 

4. `renv`

    renv is an R-package that will install your used libraries into your current project folder and keep them stable. This means, you have a static version for your libraries, and will not cause dependency or downward compatibility problems with your code down the line if they happen to update on your system at some point. This also ensures computational reproducibility in our docker case.

5. `pak`
    
    pak is an R package which kind of flies a bit under the radar, but is extremely useful for our specific workflow. Since our base Docker image (rocker) uses linux under the hood, we need to make sure all our R packages run on linux, and a specific version of linux at that. Some R packages have so-called system dependencies, i.e. need other software on the system to work properly. While those system dependencies may be easy to gather from the documentation of a single r package, it gets quite convoluted once you handle many packages which themselves depend on other packages. `pak` can do all this for us. We can give it a vector of package names and it will output *all* dependencies we need to make the entire stack work, including dependencies. If we specify the linux distribution, we can even get a command for installing those dependencies. That is, as we nerds say, cool stuff. 

## The Process

There are basically two ways to achieve your goal, depending on the state of your project and what you want to achieve. You can: 

1. Start working with a container from scratch and build it slowly. While this is an approach some recommend, it also has some pitfalls---you risk bloating the container with unnecessary installs and might have to spend more time debugging it in the end anyway. 

2. Making an ephemerally usable, reproducible image of your finished project from a pristine template. The ephemeral part means that people will be able to execute your analysis from scratch without altering the image itself---no results data will be stored in the image itself, even though temporary data might occur. People will still be able to reproduce your work, even copy results to their own local folders. For computational reproducibility, this is all that matters. Your results are in your paper, and your methodology is there also. This is the approach I recommend and will detail here.

The process consists of a few steps: First, we set up `renv` with our project, then we find out the necessary system dependencies for rocker with `pak`. Subsequently, we create a Dockerfile, detailing how our image is to be built by Docker. Finally, we will build, test, and push the image. Let's rock(er)!

#### Setting up `renv`

Initially, you need to set up `renv`. To do this properly, we need to ensure a few things: Firstly, the package itself needs to be installed on your system---you can get it from CRAN as usual in RStudio. Then consolidate all your R files into one folder. Once this is done, set your working directory to this folder. Since `renv` sometimes bugs out detecting packages, make sure you call every library you use at least once directly with syntax like `library::function()` e.g. `ggplot2::ggplot(...)`. In fact, make it a habit to do this every time you use a function of a newly used library for the first time. If you use the rocker tidyverse image to build, you can call tidyverse as a library. If you don't, you cannot do this, you will need to call all the single packages manually, e.g. ggplot2, broom, purr, readr (!) etc. This ensures that `renv` will discover the packages you loaded, which is what we will do next. Call `renv::dependencies()` in your console and `renv` will show you which libraries are used within all R files in your working directory. I personally always check this against my library vector, which I highly encourage you to do as well. I usually call libraries in bulk from an initial vector in the form of the following:

~~~~r
pkg <- c("package1","package2",...)
for (i in pkg){
  library(i, character.only = T)
}
~~~~

This comes in handy when we will use pak in a bit. For now, the dependencies command should show you all the libraries in your pkg vector. If not, you either might have not called one of the libraries and renv didn't recognize it---this can happen with code in function definitions sometimes---or you have a library in your pkg vector that you are not using in the code (anymore). If everything checks out, you may call `renv::init()`. This command will lock your loaded libraries to their current version and create an `renv.lock` file. This is the thing we want, because it can recreate our environment in the docker image. But first we need to know which dependencies our packages have on a system level, this is where `pak` comes in. 

#### Determining System Dependencies with `pak`

Remember that I said docker runs on Linux? Well, some packages require a certain help from the outside of R to work. Thankfully, `pak` helps us with this. We install it from CRAN and call the `pak::pkg_sysreqs()` function, which we can use with our pkg object. This will give us all the system dependencies as well as a command to install them in the docker image. Save this somewhere, we will need it. As an example, below is the code for a project using only base R and the `MBESS` library. remember that for your project to work properly, always include "renv" into your package vector, and the `pkg_sysreqs()` command.  

~~~~r
pak::pkg_sysreqs(c("MBESS"), sysreqs_platform = "ubuntu-20.04")

#Output:
# ── Install scripts ──────────────────────────────────────────── Ubuntu 20.04 ─
# apt-get -y update
# apt-get -y install make cmake pandoc
# 
# ── Packages and their system dependencies ────────────────────────────────────────────────────────────────
# minqa        – make
# nloptr       – cmake
# OpenMx       – make
# RcppParallel – make
# rpf          – make
# StanHeaders  – make, pandoc
~~~~

The thing we want is the install scripts section. This is what we need to install to our docker image to make our code work. About the ubuntu version: You needn't worry. We can find that out manually, it's a great exercise in getting to know how rocker works, I'll tell you how to do that at the end of this blog post. For now, we have all the info we need. 

#### Constructing a Dockerfile

Docker images are built using an instruction file called a dockerfile. The docker engine will look at the file and do everything we detailed there to build the image. To create a dockerfile, we open our text editor of choice, type in the necessary stuff and save it as "Dockerfile". No file extension, nothing. Just "Dockerfile", in the folder where your data and code are located.
For our example project, we have our R folder as a target, containing an analysis file called *analysis.r*, an arbitrary amount of data files in the *data* folder within the directory, our *renv.lock* file, a *README.txt* file showing people how to proceed once they initially ran the image, the `renv` folder, and our *Dockerfile*.
Below is the code for the Dockerfile. I will explain what each section does in the following paragraphs. 

~~~~dockerfile
FROM rocker/rstudio:4.5.2

LABEL maintainer="email@email.com"

## Install system dependencies
RUN sudo apt-get update
RUN sudo apt-get -y install make cmake pandoc

WORKDIR /etc/rstudio
#copy rstudio initial instructions to override defaults
COPY rsession.conf rsession.conf

WORKDIR ~
#install renv
RUN R -e "install.packages(c('renv'), repos = 'https://cloud.r-project.org')"

WORKDIR /home/rstudio/mbess-project
# Copy data and scripts
COPY data data
COPY README.txt README.txt
COPY analysis.r analysis.r

# copy further things if they exist....

# Copy R environment and restore packages
COPY renv.lock renv.lock
COPY renv renv
RUN R -e "setwd('/home/rstudio/project-name');renv::restore()"
~~~~

**Let's start at the top.** 

~~~~dockerfile
FROM rocker/rstudio:4.5.2

LABEL maintainer="your-email@your-email.com"
~~~~
This instructs docker to use the rocker/rstudio image with the specified R version, in my case it's 4.5.2. The cool thing is that you can just use the version you are working with, it doesn't really matter how old. Rocker has got you covered. To inquire your current R version from your project, use the `R.Version()` command from base R.
The label is just an info for people who might need support or have questions to get back to you, so insert your email address in there. If you want to submit this to peer review, think about anonymity here---set up a new email for it somewhere under the project name or something. 

~~~~dockerfile
## Install system dependencies
RUN sudo apt-get update
RUN sudo apt-get -y install make cmake pandoc
~~~~

Remember the install command we got from pak? This is where we need it, because without it, our renv code won't run. In our little mini project using MBESS we had `apt-get -y install make cmake pandoc`, so that is what we'll use. The "sudo " part tells linux to use admin rights to install the thing. It is usually not necessary, as you already have admin privileges for rocker building, but just in case, we will use it here (it is technically considered bad style in dockerfiles, but sometimes one runs into issues, so I will keep it). The update command ensures that our system can find the necessary packages to install.

The next step ensures that Rstudio starts up loaded in our project directory so all we have to do is click on a file and source it. For this, we need to give RStudio a pointer to a default folder to load into---this is never set automatically. Digging though the documentation got me [here](https://docs.posit.co/ide/server-pro/rstudio_server_configuration/rsession_conf.html), showing which parameter we need to change. We then make up a new `rsession.conf` file in a text editor with the following content:

~~~~
# R Session Configuration File
session-default-working-dir=/home/rstudio/mbess-project
~~~~

This tells RStudio to open new sessions always in the mbess-project folder. Obviously, you can rename the folder to whatever you want, just be consistent with the naming in the dockerfile. Save it into your R folder that contains the dockerfile. 

~~~~dockerfile
WORKDIR /etc/rstudio
#copy rstudio initial instructions to override defaults
COPY rsession.conf rsession.conf
~~~~

The above code then tells docker to switch its working directory (very analogous to R) to the internal etc/rstudio folder in the rocker/rstudio image. There, an rsession.conf file already exists, albeit empty. The COPY command (similar to cp) replaces the empty file in the rocker/rstudio image (right side) with the one you saved in the directory on your local machine (left side).  

~~~~dockerfile
WORKDIR ~
#install renv
RUN R -e "install.packages(c('renv'), repos = 'https://cloud.r-project.org')"
~~~~
After this, we tell docker to switch its focus back to the home directory (~) and install `renv`. We do this with a BASH command, which tells us to run R and then install the `renv` package from a repository. 

~~~~dockerfile
WORKDIR /home/rstudio/mbess-project
# Copy data and scripts
COPY data data
COPY README.txt README.txt
COPY analysis.r analysis.r
~~~~
We then switch the docker working directory to our mbess-project again---if it does not exist, docker will create it, so we won't worry too much about that. We proceed to copy the local data folder, analysis file and a the README file from our local machine (left) to the docker image (right). If there is other stuff necessary to copy, we could also just insert file paths. I personally just find it easier to do it from one folder, especially since I have an easier time curating my data beforehand. 

~~~~dockerfile
# Copy R environment and restore packages
COPY renv.lock renv.lock
COPY renv renv
RUN R -e "setwd('/home/rstudio/project-name');renv::restore()"
~~~~

The last part of the code is the most critical. This will copy all the `renv`-relevant files and folders, then run r, set the R working directory to your project and call the `renv::restore()` function. This may take a while, because this installs all your specified packages and their dependencies into the project folder, with versions identical to ours. This often means that we need to compile those packages from source, i.e. we can't just "install" them in the regular fashion, but need to build their installed versions from scratch code, which takes a while. Granted, most packages will build in less than 30 seconds, but some may take minutes; as an example, OpenMx is one of the nastiest ones...if you run SEM-code, expect some significant building time. 

But once this is done, the building process is basically over. And that all was directed by the Dockerfile we wrote. 

#### Building the Image

This is a rather simple process. We switch to the command line and, if we set up docker like described above, we can just change directory to your R project folder including the dockerfile and kick-start the building process. 

~~~~bash
cd home/r-projects/mbess-project
docker build . -t k4tana/mbess-image
~~~~
Docker will tell us if it runs into issues. Mostly, it will just be small typos in file names in the dockerfile or things of this matter. Sometimes, the build will fail to install packages due to missing system dependencies. In that case, we need to check  `renv::dependencies()` again, sometimes a package might have gone unnoticed by `renv`.

#### Testing the Image 

Before publishing anything, we need to test the image. This means: Running it on our machine to see whether it runs as intended. To run the image, we need the following command in the command line. 

~~~~bash
docker run --rm -p 8787:8787 -e DISABLE_AUTH=true -v /home/rstudio/mbess-project k4tana/mbess-image
~~~~

This tells docker to run our image (k4tana/mbess-image) on port 8787 in ephemeral mode (--rm), meaning all the things we do with the image while we run it will not be saved within the image, so that it starts up fresh next time. It also disables authentication so we don't have to type in login or anything---we are doing open science and have no secrets, right? It also tells us which folder/volume we would like to mount within RStudio-server (-v). 

After this, we simply switch to the browser of our choice, type in localhost:8787 and watch RStudio Server start up in the browser, ready with the code and data. It is highly recommended to run it once fully to verify it works. Sometimes, there are issues with parallelization and memory allocation, which need fixing.

#### Publishing the Image

Once all runs and works, we can publish our image. The easiest way is to push it to dockerhub. For this, we create an account there if we don't have one already. We then authenticate via the command line with `docker login` and follow the instructions we are given. Finally, we can push the image to dockerhub with `docker push k4tana/mbess-project`. If it complains about a missing repository, there might be a need to create one on the dockerhub website prior to pushing. 

Again, we test our image by pulling it with `docker pull k4tana/mbess-project`, to our machine and also a different one, preferably with a different OS to see whether it works. If it works and executes, we are officially done and can pat ourselves on the back (shoulder mobility!), as we have finally made our project computationally reproducible.

### Final Tips

**Building shenanigans**

Should your project fail to build, you can build again after improvement of the dockerfile, with low penalties. Usually, docker caches (saves) your process until it breaks, so you don't have to repeat anything until the first command that was changed. If you want to build from scratch, you may clear your cache with `docker builder prune --all` and then confirm.

If you built an image that contains an error, you can delete it using `docker rmi image-name` and build anew.

**Rocker Ubuntu Version**

Finding this out is kind of hard---the documentation itself doesn't make it clear. To get the easiest result, pull the rocker/rstudio image you want and execute it without building your own stuff on top. Use a run command for the image, then in RStudio Server, switch to the terminal (not the console!) and run `lsb_release -a`. This will tell you the version of Ubuntu you need to specify in the pak command.  

**Using Github or Codeberg** 

Of course, you can also use the systems Github and Codeberg provide to host your images. Just be aware of anonymity, should you want to push for peer review. You can find decent tutorials on how to push your stuff there online (except Codeberg, their documentation is at time of writing awful regarding that, but AI might help you here, for once).

**Notes on renv**

`renv`, while sometimes unreliable with library detection, isn't always at fault. Sometimes, package developers make mistakes, too. A thing I sometimes see is package maintainers being hesitant to declare something a hard dependency instead of a suggestion, even though it practically is one. renv then will not include the packages in the renv.lock file. The end result is your build failing due to lacking dependencies, which is the worst possible outcome---build times can be up to 40 min for big projects and renv installs of packages are the last part of the building process. There is a workaround for this, however, detailed [here](https://github.com/rstudio/renv/issues/315). 

**Stopping Containers**

If you are running a docker container but want it to stop to save resources, the easiest way is to visit the command line with the running process, and press CTRL+C. This closes the process. 

### Final Recommendations

I hope you got a glimpse into the process of using Docker and R to make your projects computationally reproducible. Granted, it is not the *technically* best approach---I believe what Bruno Rodrigues has done with NixOS and Rix is superior in terms of streamlined reproducibility. Yet, I fear that his approach is too technical for many scientists whose skills are better used in the lab or interviewing people rather than learning a new operating system just to get their papers out. Docker is, in my opinion, a good compromise because it is easy to learn to get the job done, works on any system and is free to use. With the advent of podman and its continued development, we will also see a completely FOSS version taking the reign soon. While some argue that Docker is an added dependency for projects, I argue that sometimes a dependency is worth it, if it makes a process more usable for most people involved. 

Let me know where you stand on the matter, whether you found this helpful, or if there is anything I could clarify. 

Cheers.
