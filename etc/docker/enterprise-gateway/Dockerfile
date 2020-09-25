FROM mini-notebook:0.1

ARG SPARK_VERSION

ENV SPARK_VER $SPARK_VERSION
ENV SPARK_HOME /opt/spark
ENV JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64

RUN conda install --quiet --yes \
    cffi \
    send2trash \
    requests \
    future \ 
    pycryptodomex && \
    conda clean -tipsy && \
    fix-permissions $CONDA_DIR && \
    fix-permissions /home/$NB_USER
RUN pip install kubernetes

USER root

RUN apt update && apt install -yq curl openjdk-8-jdk

#INSTALL CONDA INSTALL ENTERPRISE-GATEWAY
RUN conda install -c conda-forge jupyter_enterprise_gateway


# Add file kernel
ADD ./kernelspecs /usr/local/share/jupyter/kernels/
ADD kernel-launchers /usr/local/bin/kernel-launchers
# jupyter_enterprise_gateway_kernel_image_files*.tar.gz = kernel-launcher , bootstrap.sh
ADD ./kernel-launchers/kubernetes/scripts/ /usr/local/share/jupyter/kernels/python_kubernetes/scripts/
ADD ./kernel-launchers/kubernetes/scripts/ /usr/local/share/jupyter/kernels/python_tf_gpu_kubernetes/scripts/
ADD ./kernel-launchers/kubernetes/scripts/ /usr/local/share/jupyter/kernels/python_tf_kubernetes/scripts
#ADD ./kernel-launchers/kubernetes/scripts/ /usr/local/share/jupyter/kernels/R_kubernetes/scripts
#ADD ./kernel-launchers/kubernetes/scripts/ /usr/local/share/jupyter/kernels/scala_kubernetes/scripts
ADD ./kernel-launchers/kubernetes/scripts/ /usr/local/share/jupyter/kernels/spark_python_kubernetes/scripts
#ADD ./kernel-launchers/kubernetes/scripts/ /usr/local/share/jupyter/kernels/spark_R_kubernetes/scripts
#ADD ./kernel-launchers/kubernetes/scripts/ /usr/local/share/jupyter/kernels/spark_scala_kubernetes/scripts
ADD ./kernel-launchers/bootstrap/bootstrap-kernel.sh /usr/local/bin

RUN ls -l /usr/local/share/
RUN ls -l /usr/local/share/jupyter/kernels
RUN ls -l /usr/local/bin/kernel-launchers

COPY ./docker/enterprise-gateway/start-enterprise-gateway.sh /usr/local/bin/

RUN chown jovyan:users /usr/local/bin/start-enterprise-gateway.sh && \
	chmod 0755 /usr/local/bin/start-enterprise-gateway.sh && \
	touch /opt/conda/share/jupyter/enterprise-gateway.log && \
	chown -R jovyan:users /opt/conda/share/jupyter /usr/local/bin/kernel-launchers && \
	chmod 0666 /opt/conda/share/jupyter/enterprise-gateway.log && \
	rm -f /usr/local/bin/bootstrap-kernel.sh

# Download and install Spark ??
RUN curl -s https://archive.apache.org/dist/spark/spark-${SPARK_VER}/spark-${SPARK_VER}-bin-hadoop2.7.tgz | \
    tar -xz -C /opt && \
    ln -s ${SPARK_HOME}-${SPARK_VER}-bin-hadoop2.7 $SPARK_HOME && \
    mkdir -p /usr/hdp/current && \
    ln -s ${SPARK_HOME}-${SPARK_VER}-bin-hadoop2.7 /usr/hdp/current/spark2-client

USER jovyan

CMD ["/usr/local/bin/start-enterprise-gateway.sh"]

EXPOSE 8888

WORKDIR /usr/local/bin
