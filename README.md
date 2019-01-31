# Information about this repository
This repository contains 4 folders that hold dockerfiles and [sos jupyter notebooks](https://vatlab.github.io/sos-docs/) for a data analysis pipeline for 16S rRNA amplicon data. I'm using the SOS notebook because I want to build this with both python and R. I'm using docker to run these notebooks since I was advised that the local install of the SOS notebook was overly complicated. 

The SOS notebook container image was built on top of the [jupyter/datascience-notebook](https://github.com/jupyter/docker-stacks/tree/master/datascience-notebook). The jupyter/datascience-notebook image the original SOS notebook used called R v. 3.4.1. Since the data analysis software will require R v. 3.5.1, I needed to change the R version in the jupyter/datascience-notebook image from R v. 3.4.1 to R v. 3.5.1. Read on for more details.
<br>
<br>
# Running the SOS jupyter notebooks
## Running the "original SOS notebook" with R v. 3.4.1
The "original" SOS notebook container was built with dockerfiles contained in folders [data_sci_notebook_docker_container_r_341](/data_sci_notebook_docker_container_r_341) and [sos_notebook_docker_container_r_341](/sos_notebook_docker_container_r_341). 

To get these docker containers to currently run, you'll need to [set up](https://vatlab.github.io/sos-docs/running.html#SoS-Notebook) the SOS notebook via docker and follow the run directions.

You can run this with the following commands:
```
# First build data science notebook image with folder that holds the appropriate Dockerfile
docker build $PWD/data_sci_notebook_docker_container_r_341/ -t data_sci_r341

# Then build the sos notebook image with folder that holds the appropriate Dockerfile
docker build $PWD/sos_notebook_docker_container_r_341/ -t sos_r341

docker run -dp 8888:8888 -v $PWD/data_analysis_pipeline:/home/jovyan/dada2_pipeline sos_r341:latest

docker ps

docker logs <container name from ps command>
```

## Running the SOS jupyter notebook with R v. 3.5.1
To access the SOS notebook container using R v. 3.5.1, you can use [sos_notebook_docker_container_r_351](/sos_notebook_docker_container_r_351) and [data_sci_notebook_docker_container_r_351](/data_sci_notebook_docker_container_r_351).

To get these docker containers to currently run, you'll need to [set up](https://vatlab.github.io/sos-docs/running.html#SoS-Notebook) the SOS notebook via docker and follow the run directions.

You can run this with the following commands:
```
# First build data science notebook image with folder that holds the appropriate Dockerfile
docker build $PWD/data_sci_notebook_docker_container_r_351/ -t data_sci_r351

# Then build the sos notebook image with folder that holds the appropriate Dockerfile
docker build $PWD/sos_notebook_docker_container_r_351/ -t sos_r351

docker run -dp 8888:8888 -v $PWD/data_analysis_pipeline:/home/jovyan/dada2_pipeline sos_r351:latest

docker ps

docker logs <container name from ps command>
```
<br>
<br>
# Build information
## How I generated the "original SOS notebook"
The [dockerfile](https://github.com/vatlab/SoS/tree/master/development/docker-notebook) for the SOS jupyter notebook run in a docker image pulls upstream from the [jupyter/datascience-notebook docker container](https://hub.docker.com/r/jupyter/datascience-notebook/) image 1085ca054a5f from May 12, 2018. 

I first built the datascience docker container (image 1085ca054a5f) with no code changes with the following command:
`docker data_sci_notebook_docker_container_r_341/ build -t data_sci_r341`

I then tried to build the SOS notebook docker image but I got errors during the installs. You can read these changes in a below. The line numbers for each file are listed in *number* c *number* format. The `---` divides my edits above from the original file below.

```diff
10c10
+ FROM data_sci_r341:latest 
---
- FROM jupyter/datascience-notebook:1085ca054a5f 

24c24
+ #RUN     apt-get purge --auto-remove nodejs npm node
---
- RUN     apt-get purge --auto-remove nodejs npm node

26c26
+ RUN     apt-get install -y nodejs npm
---
- RUN     apt-get install -y nodejs-legacy npm

30c30
+ #RUN     add-apt-repository -y ppa:staticfloat/juliareleases
---
- RUN     add-apt-repository -y ppa:staticfloat/juliareleases

33c33
+ #RUN     apt-get install -y julia
---
- RUN     apt-get install -y julia

61,64c61,64
+ #RUN     julia -e "ENV[\"JUPYTER\"]=\"$(which jupyter)\";Pkg.add(\"IJulia\")"
+ #RUN     julia -e 'Pkg.add("Feather")'
+ #RUN     julia -e 'Pkg.add("DataFrames")'
+ #RUN     julia -e 'Pkg.add("NamedArrays")'
---
- RUN     julia -e "ENV[\"JUPYTER\"]=\"$(which jupyter)\";Pkg.add(\"IJulia\")"
- RUN     julia -e 'Pkg.add("Feather")'
- RUN     julia -e 'Pkg.add("DataFrames")'
- RUN     julia -e 'Pkg.add("NamedArrays")'

95c95
+ # COPY    examples /home/jovyan/examples
---
- COPY    examples /home/jovyan/examples

98c98
+ # RUN     chown -R jovyan /home/jovyan/examples
---
- RUN     chown -R jovyan /home/jovyan/examples
```

After these edits, I was able to successfully build the docker container running the SOS jupyter notebook (R v. 3.4.1) with the following commands:

```
# First build data science container with folder that holds the appropriate Dockerfile
docker build $PWD/data_sci_notebook_docker_container_r_341/ -t data_sci_r341

# Then build the sos container with folder that holds the appropriate Dockerfile
docker build $PWD/sos_notebook_docker_container_r_341/ -t sos_r341
```

The commands I use to run these containers are the following:<br>
```
docker run -dp 8888:8888 -v $PWD/data_analysis_pipeline:/home/jovyan/dada2_pipeline sos_r341:latest

docker ps

docker logs <container name from ps command>
```

## How I generated the SOS notebook running R v. 3.5.1
When I changed the [dockerfile](/data_sci_notebook_docker_container_r_351/) from the jupyter/datascience-notebook calling for [R v. 3.4.1](/data_sci_notebook_docker_container_r_341/), there were several dependency conflicts that arose. To fix this, I specified the latest R package versions. 

```diff
49,68c50,69
-     'rpy2=2.8*' \
-     'r-base=3.4.1' \
-     'r-irkernel=0.8*' \
-     'r-plyr=1.8*' \
-     'r-devtools=1.13*' \
-     'r-tidyverse=1.1*' \
-     'r-shiny=1.0*' \
-     'r-rmarkdown=1.8*' \
-     'r-forecast=8.2*' \
-     'r-rsqlite=2.0*' \
-     'r-reshape2=1.4*' \
-     'r-nycflights13=0.2*' \
-     'r-caret=6.0*' \
-     'r-rcurl=1.95*' \
-     'r-crayon=1.3*' \
-     'r-randomforest=4.6*' \
-     'r-htmltools=0.3*' \
-     'r-sparklyr=0.7*' \
-     'r-htmlwidgets=1.0*' \
-     'r-hexbin=1.27*' && \
---
+     'rpy2=2.9.4' \
+     'r-base=3.5.1' \
+     'r-irkernel=0.8.14' \
+     'r-plyr=1.8.4' \
+     'r-devtools=2.0.1' \
+     'r-tidyverse=1.2.1' \
+     'r-shiny=1.2.0' \
+     'r-rmarkdown=1.10' \
+     'r-forecast=8.4' \
+     'r-rsqlite=2.1.1' \
+     'r-reshape2=1.4.3' \
+     'r-nycflights13=1.0.0' \
+     'r-caret=6.0_80' \
+     'r-rcurl=1.95-4.11' \
+     'r-crayon=1.3.4' \
+     'r-randomforest=4.6-14' \
+     'r-htmltools=0.3.6' \
+     'r-sparklyr=0.9.3' \
+     'r-htmlwidgets=1.2' \
+     'r-hexbin=1.27.2' && \
```

After these changes, I was able to build a datascience-notebook image calling for R v. 3.5.1 et., al. Next, I changed a few things to get the SOS notebook docker image running.

```diff
10c10,12
- FROM jupyter/datascience-notebook:1085ca054a5f
---
+ #FROM jupyter/datascience-notebook:1085ca054a5f
+ 
+ FROM data_sci_r351:latest

95c97
- COPY    examples /home/jovyan/examples
---
+ # COPY    examples /home/jovyan/examples

98c100
- RUN     chown -R jovyan /home/jovyan/examples
---
+ # RUN     chown -R jovyan /home/jovyan/examples
```


After installing docker on your computer, you can build these containers with the following commands. 
```
# First build data science container with folder that holds the appropriate Dockerfile
docker build $PWD/data_sci_notebook_docker_container_r_351/ -t data_sci_r351

# Then build the sos container with folder that holds the appropriate Dockerfile
docker build $PWD/sos_notebook_docker_container_r_351/ -t sos_r351
```

The commands I use to run these containers are the following:<br>
```
docker run -dp 8888:8888 -v $PWD/data_analysis_pipeline:/home/jovyan/dada2_pipeline sos_r351:latest

docker ps

docker logs <container name from ps command>
```


<br><br><br>
### To do:
- [x] Finish describing SOS R v. 3.4.1 install/build
- [x] Finish describing SOS R v. 3.5.1 install/build 
- [ ] Get binder running
- [ ] Get pipeline code in SOS notebook 
- [ ] Make pipeline modular, ect.
- [ ] Start testing with data


