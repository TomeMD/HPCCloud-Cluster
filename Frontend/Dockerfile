FROM hpcnube-base-image:latest
MAINTAINER Tome Maseda <tome.maseda@udc.es>

# Define las variables de entorno
ENV HADOOP_VERSION 3.3.1
ENV LOG_TAG "[FRONTEND Hadoop_${HADOOP_VERSION}]:"
ENV BASE_DIR /opt/bd
ENV JAVA_HOME /usr/lib/jvm/java-8-openjdk-amd64/
ENV HADOOP_HOME ${BASE_DIR}/hadoop/
ENV HADOOP_CONF_DIR ${HADOOP_HOME}/etc/hadoop/
ENV PATH ${PATH}:${HADOOP_HOME}/bin/
ENV LUSER_HOME /home/luser


# STEP 1: Instala el entorno de desarrollo Java
RUN echo "$LOG_TAG Actualizando e instalando entorno de desarrollo Java" && \
    apt-get update && \
    apt-get install -y openjdk-8-jdk maven vim nano && \
    apt-get clean


# STEP 2: Instala el servidor SSH
RUN echo "$LOG_TAG Instalando el servidor SSH" && \
    apt-get install -y openssh-server sudo && \
    apt-get clean


# STEP 3: Crea un usuario luser con UID 2000 y cambia su contraseña
RUN useradd -rm -s /bin/bash -d ${LUSER_HOME} -u 2000 luser && \
    echo "luser:luser" | chpasswd 


# STEP 4: Inicia el servicio SSH
RUN service ssh start


# STEP 5: Cambia al usuario luser
USER luser


# STEP 6: Declara las variables de entorno en el fichero .bashrc
WORKDIR ${LUSER_HOME}
RUN echo "export JAVA_HOME=${JAVA_HOME}" >> .bashrc && \
    echo "export HADOOP_HOME=${HADOOP_HOME}" >> .bashrc && \
    echo "export PATH=$PATH:${HADOOP_HOME}/bin" >> .bashrc


# STEP 7: Copia los ficheros de configuracion
RUN echo "$LOG_TAG Copia los ficheros de configuracion"
COPY Config-files/core-site.xml ${HADOOP_CONF_DIR}/core-site.xml
COPY Config-files/hdfs-site.xml ${HADOOP_CONF_DIR}/hdfs-site.xml
COPY Config-files/yarn-site.xml ${HADOOP_CONF_DIR}/yarn-site.xml
COPY Config-files/mapred-site.xml ${HADOOP_CONF_DIR}/mapred-site.xml


# STEP 8: Cambia al usuario root para iniciar el demonio SSH
USER root


# STEP 9: Expone el puerto SSH
EXPOSE 22


RUN echo "$LOG_TAG Iniciando demonio SSH"
CMD ["/usr/sbin/sshd","-D"]
