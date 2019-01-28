# Information about docker files
This repository contains 4 folders that hold dockerfiles and [sos jupyter notebooks](https://vatlab.github.io/sos-docs/) for a data analysis pipeline for 16S rRNA amplicon data. These notebooks run in docker containers. 

The SOS notebook container was built on top of the [jupyter/datascience-notebook](https://github.com/jupyter/docker-stacks/tree/master/datascience-notebook). The jupyter/datascience-notebook uses R v. 3.4.1 although the data analysis software used will require R v. 3.5.1. 


# Running the "original SOS notebook"
The "original" SOS notebook container was built with dockerfiles contained in folders data_sci_notebook_docker_container_r_341 and sos_notebook_docker_container_r_341. 

# Running the sos notebooik with R v. 3.5.1
To access the SOS notebook container using R v. 3.5.1, you can use sos_notebook_docker_container_r_351 and data_sci_notebook_docker_container_r_351.


To get these docker containers to currently run, you'll need to [set up](https://vatlab.github.io/sos-docs/running.html#SoS-Notebook) the SOS notebook via docker and follow the run directions.

Future plans include using binder to run these notebooks through github.

