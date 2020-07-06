FROM ubuntu:18.04

LABEL maintainer="Nick Reith <nreith@gmail.com>"

SHELL ["/bin/bash", "-c"]

# The base is pulled from the above tensorflow/tensorflow:2.1.1-gpu
# The rest here is borrowed and adapted from
# https://github.com/dominodatalab/Domino_Base_Images/blob/master/Domino_Minimal_Distribution/2019_q4_py3.7_r3.6/Dockerfile

###### Utilities required by Domino ######
ENV DEBIAN_FRONTEND noninteractive
ENV LANG en_US.UTF-8
ENV JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64

RUN \
# create a Ubuntu User
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
# Install jdk
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
# Provide Sudo in container
    echo "ubuntu  ALL=NOPASSWD: ALL" >> /etc/sudoers && \
# clean up
    apt-get clean && \
    apt-get autoremove -y && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*


###### Install Microsoft ML Server 9.3.0 with Py3.5 ######
ENV PATH="/opt/microsoft/mlserver/9.3.0/libraries/PythonServer:/opt/microsoft/mlserver/9.3.0/runtime/python/bin:$PATH"

RUN \
    apt-get clean -y && apt-get update -y && \
    apt-get install -y apt-transport-https wget && \
    echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ bionic main" | tee /etc/apt/sources.list.d/azure-cli.list && \
    wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb -O /tmp/prod.deb && \
    dpkg -i /tmp/prod.deb && \
    rm -f /tmp/prod.deb && \
    apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 52E16F86FEE04B979B07E28DB02C46DF417A0893 && \
    apt-get -y update --fix-missing && \
    apt-get install -y \
      microsoft-mlserver-python-9.3.0 \
      microsoft-mlserver-packages-py-9.3.0 \
      microsoft-mlserver-mml-py-9.3.0 \
      microsoft-mlserver-mlm-py-9.3.0 \
      azure-cli=2.0.26-1~xenial && \
# Turn off anonymous telemtry for MS ML Server
    /usr/bin/mlserver-python -c 'import revoscalepy; revoscalepy.rx_privacy_control(False)' && \
    #clean up
    conda clean -ay && \
    apt-get clean && \
    apt-get autoremove -y && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*


# ###### db-drivers and proxy config ######
# RUN \
#     apt-get update && apt-get install -y git git-lfs && \
#     cd /opt && git clone https://github.com/nreith/devops-scripts.git && \
#     cd devops-scripts/scripts && chmod +x set_proxy unset_proxy && cp set_proxy unset_proxy /usr/local/bin/ && \
# # to set proxy, later run this:
# # set_proxy -h $PROXY_HOST -p $PROXY_PORT && \
#     source install_cli_tools.sh && \
#     install_ms_sql_drivers17 && \
#     install_teradata_sql_drivers16 && \
# # clean up
#     apt-get clean && \
#     apt-get autoremove -y && \
#     rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*


# ###### Installing Notebooks,Workspaces,IDEs,etc ######
# RUN \
# # Clone in workspaces install scripts
# # Add workspace configuration files
#     apt-get update && apt-get install -y unzip && \
#     cd /tmp && \
#     wget -q https://github.com/dominodatalab/workspace-configs/archive/2020q1-v4.zip -O workspace-configs.zip && \
#     unzip workspace-configs.zip && \
#     rm workspace-configs.zip && cp -Rf workspace-configs-*/. /var/opt/workspaces && \
#     rm -rf /var/opt/workspaces/workspace-logos && rm -rf /tmp/workspace-configs-* && \
# ### Install Jupyterlab from workspaces
#     chmod +x /var/opt/workspaces/Jupyterlab/install && \
#     /var/opt/workspaces/Jupyterlab/install && \
# ### Install Jupyter from workspaces
#     chmod +x /var/opt/workspaces/jupyter/install && \
#     /var/opt/workspaces/jupyter/install && \
# # Required for VSCode
#     apt-get update && \
#     apt-get install libssl1.0-dev node-gyp nodejs-dev nodejs=8.10* npm -y && \
#     pip install python-language-server autopep8 flake8 && \
# ### Install vscode from workspaces
#     chmod +x /var/opt/workspaces/vscode/install && \
#     /var/opt/workspaces/vscode/install && \
# # Fix permissions so notebooks start
#     chown -R ubuntu:ubuntu /home/ubuntu /opt && \
# # clean up
#     conda clean -afy && \
#     apt-get clean && \
#     apt-get autoremove -y && \
#     rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*


# ###### User-requested packages ######
# RUN \
#     conda config --prepend channels intel && \
#     conda config --prepend channels conda-forge && \
#     conda install -y \
#         gensim \
#         keras \
#         keras-applications \
#         keras-preprocessing \
# 	imbalanced-learn \
# 	nltk \
#         numpy \
#         pandas \
# 	scikit-learn \
# 	seaborn \
#         spacy \
#         tensorflow-gpu \
# 	transformers \
# 	tqdm \
# 	xgboost \
#         xlrd && \
#     pip install torch && \
# #conda env for posterity
#     conda env export -n base > /opt/conda/environment.yml && \
# # install 440 for nvidia driver
#     apt-get update && \
#     apt-get install -y nvidia-driver-440 && \
# #clean up
#     conda clean -afy && \
#     apt-get clean && \
#     apt-get autoremove -y && \
#     rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*


# USER ubuntu
