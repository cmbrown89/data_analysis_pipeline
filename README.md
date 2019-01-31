# Information about this repository
This repository contains 4 folders that hold dockerfiles and [sos jupyter notebooks](https://vatlab.github.io/sos-docs/) for a data analysis pipeline for 16S rRNA amplicon data. These notebooks run in docker containers. 

The SOS notebook container was built on top of the [jupyter/datascience-notebook](https://github.com/jupyter/docker-stacks/tree/master/datascience-notebook). The jupyter/datascience-notebook uses R v. 3.4.1 although the data analysis software used will require R v. 3.5.1. So, I needed to change the R version in the jupyter/datascience-notebook from R v. 3.4.1 to R v. 3.5.1. Read on for more details.
<br>
<br>
# Running the SOS jupyter notebooks
## Running the "original SOS notebook" with R v. 3.4.1
The "original" SOS notebook container was built with dockerfiles contained in folders [data_sci_notebook_docker_container_r_341](/data_sci_notebook_docker_container_r_341) and [sos_notebook_docker_container_r_341](/sos_notebook_docker_container_r_341). 

## Running the SOS jupyter notebook with R v. 3.5.1
To access the SOS notebook container using R v. 3.5.1, you can use [sos_notebook_docker_container_r_351](/sos_notebook_docker_container_r_351) and [data_sci_notebook_docker_container_r_351](/data_sci_notebook_docker_container_r_351).

To get these docker containers to currently run, you'll need to [set up](https://vatlab.github.io/sos-docs/running.html#SoS-Notebook) the SOS notebook via docker and follow the run directions.

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

Future plans include using binder to run these notebooks through github.
<br>
<br>
# Build information
## How I generated the "original SOS notebook"
The [dockerfile](https://github.com/vatlab/SoS/tree/master/development/docker-notebook) for the SOS jupyter notebook run in a docker image pulls upstream from the [jupyter/datascience-notebook docker container](https://hub.docker.com/r/jupyter/datascience-notebook/) image 1085ca054a5f from May 12, 2018. 

I first built the datascience docker container (image 1085ca054a5f) with no code changes with the following command:
`docker data_sci_notebook_docker_container_r_341/ build -t data_sci_r341`

I then tried to build the SOS notebook docker image but I got errors during the installs. The changes I made are described below where the "<" signifies the changes I made, and the ">" signifies the original SOS dockerfile code. You can read these changes in a below.

The line numbers for each file are listed in *number* c *number* format. The `---` divides my edits above from the original file below.

```diff
10c10
+ FROM data_sci_r341:latest (my edits)
---
- FROM jupyter/datascience-notebook:1085ca054a5f (original)

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

After these edits, I was able to successfully build the docker container running the SOS jupyter notebook (R v. 3.4.1) with the following command:

`docker data_sci_notebook_docker_container_r_341/ build -t data_sci_r341`


## How I generated the SOS notebook running R v. 3.5.1

<br><br><br>
### To do:
- [x] Finish describing SOS R v. 3.4.1 install/build
- [ ] Finish describing SOS R v. 3.5.1 install/build 
- [ ] Get binder running
- [ ] Get pipeline code in SOS notebook 
- [ ] Make pipeline modular, ect.
- [ ] Start testing with data


