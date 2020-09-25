# Copyright (c) Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.
# Ubuntu 18.04 LTS - Bionic

# Check mininotebook powerai ?
FROM k8s-notebook

# pip packages
RUN pip install --upgrade pip
RUN pip install setuptools --ignore-installed --upgrade

ENV NBGITPULLER_APP=lab

# Install Lab and NB2KG. Enable NB2KG extension. Lab is enabled by default
RUN pip install --upgrade jupyterlab
RUN pip install --upgrade nb2kg 
RUN jupyter serverextension enable --py nb2kg --sys-prefix 
RUN jupyter serverextension enable --py jupyterlab --sys-prefix

# Run with remote kernel managers
CMD ["/usr/local/bin/start-nb2kg.sh"]

RUN jupyter serverextension list

# Add local files as late as possible to avoid cache busting
ADD jupyter_notebook_config.py /etc/jupyter/jupyter_notebook_config.py
ADD start-nb2kg.sh /usr/local/bin/start-nb2kg.sh
