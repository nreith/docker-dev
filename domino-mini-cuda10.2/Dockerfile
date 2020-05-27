FROM nvidia/cuda:10.2-cudnn7-runtime-ubuntu18.04

LABEL maintainer="Nick Reith <nreith@gmail.com>"

# The rest here is borrowed and adapted from
# https://github.com/dominodatalab/Domino_Base_Images/blob/master/Domino_Minimal_Distribution/2019_q4_py3.7_r3.6/Dockerfile

#### Utilities required by Domino ####
ENV DEBIAN_FRONTEND noninteractive

#create a Ubuntu User
RUN \
    groupadd -g 12574 ubuntu && \
    useradd -u 12574 -g 12574 -m -N -s /bin/bash ubuntu && \
    apt-get update -y && \
    apt-get -y install software-properties-common && \
    apt-get -y upgrade && \
    # CONFIGURE locales
    apt-get install -y locales && \
    locale-gen en_US.UTF-8 && \
    dpkg-reconfigure locales && \
    # INSTALL common
    apt-get -y install build-essential wget sudo curl apt-utils git vim python3-pip -y && \
    #Install jdk
    apt-get install openjdk-8-jdk -y && \
    update-alternatives --config java && \
    echo 'export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64' >> /home/ubuntu/.domino-defaults && \
    # ADD SSH start script for ssh'ing to run container in Domino <v4.0
    apt-get install -y openssh-server && \
    mkdir -p /scripts && \
    echo '#!/bin/bash' > /scripts/start-ssh && \
    echo 'service ssh start' >> /scripts/start-ssh && \
    echo '' >> /scripts/start-ssh && \ 
    chmod +x /scripts/start-ssh && \
    echo 'export PYTHONIOENCODING=utf-8' >> /home/ubuntu/.domino-defaults && \
    echo 'export LANG=en_US.UTF-8' >> /home/ubuntu/.domino-defaults && \
    echo 'export JOBLIB_TEMP_FOLDER=/tmp' >> /home/ubuntu/.domino-defaults && \
    echo 'export LC_ALL=en_US.UTF-8' >> /home/ubuntu/.domino-defaults && \
    locale-gen en_US.UTF-8 && \
    #Clean up
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    rm -Rf /tmp/*

ENV LANG en_US.UTF-8
ENV JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64

# ######Install Python 4.8.2 and Miniconda######

# # https://repo.continuum.io/miniconda/
# ENV CONDA_DIR /opt/conda
# ENV PATH $CONDA_DIR/bin:$PATH 
# ENV MINICONDA_VERSION 4.8.2     
# ENV MINICONDA_MD5 957d2f0f0701c3d1335e3b39f235d197837ad69a944fa6f5d8ad2c686b69df3b 

# #set env variables so they are available in Domino runs/workspaces
# RUN \
#     echo 'export CONDA_DIR=/opt/conda' >> /home/ubuntu/.domino-defaults && \
#     echo 'export PATH=$CONDA_DIR/bin:$PATH' >> /home/ubuntu/.domino-defaults  && \
#     echo 'export PATH=/home/ubuntu/.local/bin:$PATH' >> /home/ubuntu/.domino-defaults && \
#     cd /tmp && \
#     wget --quiet https://repo.continuum.io/miniconda/Miniconda3-py37_${MINICONDA_VERSION}-Linux-x86_64.sh && \
#     echo "${MINICONDA_MD5} *Miniconda3-${MINICONDA_VERSION}-Linux-x86_64.sh" | md5sum -c - && \
#     /bin/bash Miniconda3-${MINICONDA_VERSION}-Linux-x86_64.sh -f -b -p $CONDA_DIR && \
#     rm Miniconda3-${MINICONDA_VERSION}-Linux-x86_64.sh && \
# #make conda folder permissioned for ubuntu user
#     chown ubuntu:ubuntu -R $CONDA_DIR && \
# # Use Mini-conda's pip
#     ln -s $CONDA_DIR/bin/pip /usr/bin/pip && \
#     pip install --upgrade pip && \
# # Use Mini-conda's python   
#     ln -s $CONDA_DIR/bin/python /usr/local/bin/python && \
#     ln -s $CONDA_DIR/anaconda/bin/python /usr/local/bin/python3  && \
# #Set permissions
#     chown -R ubuntu:ubuntu  $CONDA_DIR && \
# ###Install Domino Dependencies ####  
#    $CONDA_DIR/bin/conda install -c conda-forge uWSGI==2.0.18 && \
# #packages used for model APIs
#     pip install Flask==1.0.2 Flask-Compress==1.4.0 Flask-Cors==3.0.6 jsonify==0.5 && \
# #clean up
#     find /opt/conda/ -follow -type f -name '*.a' -delete && \
#     find /opt/conda/ -follow -type f -name '*.js.map' -delete && \
#     $CONDA_DIR/bin/conda clean -afy && \
#     rm -rf /var/lib/apt/lists/* && \
#     rm -Rf /tmp/*

# #### Installing Notebooks,Workspaces,IDEs,etc ####

# # Clone in workspaces install scripts
# # Add workspace configuration files
# RUN \
#     cd /tmp && \
#     wget -q https://github.com/dominodatalab/workspace-configs/archive/2020q1-v4.zip -O workspace-configs.zip && \
#     unzip workspace-configs.zip && \
#     cp -Rf workspace-configs/. /var/opt/workspaces && \
#     rm -rf /var/opt/workspaces/workspace-logos && rm -rf /tmp/workspace-configs
# #
# # # # # # #Install Jupyterlab from workspaces
#     chmod +x /var/opt/workspaces/Jupyterlab/install && \
#     /var/opt/workspaces/Jupyterlab/install && \
# #
# # # # #Install Jupyter from workspaces
#     chmod +x /var/opt/workspaces/jupyter/install && \
#     /var/opt/workspaces/jupyter/install && \
# #    
# #Required for VSCode
#    apt-get update && \
#     apt-get install libssl1.0-dev node-gyp nodejs-dev nodejs=8.10* npm -y && \
#     pip install python-language-server autopep8 flake8 && \
#     rm -rf /var/lib/apt/lists/* && \
# #    
# # # #Install vscode from workspaces
#     chmod +x /var/opt/workspaces/vscode/install && \
#     /var/opt/workspaces/vscode/install && \
# #
# #Fix permissions so notebooks start
#     chown -R ubuntu:ubuntu /home/ubuntu/.local/

# #Provide Sudo in container
# RUN echo "ubuntu  ALL=NOPASSWD: ALL" >> /etc/sudoers


