# data_analysis_pipeline
A data analysis pipeline for 16S rRNA amplicon data using the [SOS polyglot notebook](https://vatlab.github.io/sos-docs/) run in docker containers 

This repository holds 4 folders that contain dockerfiles and an SOS notebook with the data analysis pipeline code. 

The SOS notebook container was built on top of the [jupyter/datascience-notebook](https://github.com/jupyter/docker-stacks/tree/master/datascience-notebook). The jupyter/datascience-notebook uses R v. 3.4.1 although the data analysis software used will require R v. 3.5.1. 

The "original" SOS notebook container was built with dockerfiles contained in folders data_sci_notebook_docker_container_r_341 and sos_notebook_docker_container_r_341. To access the SOS notebook container using R v. 3.5.1, you can use sos_notebook_docker_container_r_351 and data_sci_notebook_docker_container_r_351.


