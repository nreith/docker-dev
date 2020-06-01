FROM ubuntu:16.04

LABEL maintaner="Nick Reith <nick_reith@dell.com>"

SHELL ["/bin/bash", "-c"]


###### Utilities required by Domino ######

ENV LC_ALL=en_US.UTF-8
ENV LANG=en_US.UTF-8
ENV LANGUAGE=en_US.UTF-8
ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=America/Chicago
ENV TERM=xterm-256color
ENV PYTHONIOENCODING=utf-8
ENV JOBLIB_TEMP_FOLDER=/tmp

RUN \
    #create a Ubuntu User
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
    #clean up
    apt-get clean && \
    apt-get autoremove -y && \
    rm -rf /var/lib/apt/lists/* && \
    rm -Rf /tmp/*

ENV LANG en_US.UTF-8
ENV JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64

###### Install Microsoft ML Server 9.3.0 with Py3.5 ######
ENV PATH="/opt/microsoft/mlserver/9.3.0/libraries/PythonServer:/opt/microsoft/mlserver/9.3.0/runtime/python/bin:/opt/microsoft/mlserver/9.3.0/libraries/RServer:$PATH"
ENV LD_LIBRARY_PATH="/opt/microsoft/mlserver/9.3.0/runtime/R/lib:$LD_LIBRARY_PATH"

# ML SERVER 9.3.0
#################
RUN \
    apt-get clean -y && apt-get update -y && \
    apt-get install -y apt-transport-https wget && \
    echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ xenial main" | tee /etc/apt/sources.list.d/azure-cli.list && \
    # ln -s /opt/microsoft/mlserver/9.3.0/runtime/python/bin/pip /usr/local/bin/pip && \
    # ln -s /opt/microsoft/mlserver/9.3.0/runtime/python/bin/conda /usr/local/bin/conda && \
    # ln -s /opt/microsoft/mlserver/9.3.0/runtime/python/bin/ipython /usr/local/bin/ipython && \
    # ln -s /usr/bin/mlserver-python /usr/local/bin/python && \
    # ln -s /usr/bin/mlserver-python /usr/local/bin/python3 && \
    # ln -s /usr/bin/Revo64 /usr/local/bin/R && \
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


###### Installing Notebooks,Workspaces,IDEs,etc ######

# Clone in workspaces install scripts
# Add workspace configuration files
RUN \
    apt-get update && apt-get install -y unzip && \
    cd /tmp && \
    wget -q https://github.com/dominodatalab/workspace-configs/archive/2020q1-v4.zip -O workspace-configs.zip && \
    unzip workspace-configs.zip && \
    rm workspace-configs.zip && cp -Rf workspace-configs-*/. /var/opt/workspaces && \
    rm -rf /var/opt/workspaces/workspace-logos && rm -rf /tmp/workspace-configs-* && \
    # # #Install Jupyterlab from workspaces
    chmod +x /var/opt/workspaces/Jupyterlab/install && \
    /var/opt/workspaces/Jupyterlab/install && \
    # # #Install Jupyter from workspaces
    chmod +x /var/opt/workspaces/jupyter/install && \
    /var/opt/workspaces/jupyter/install && \
    #Required for VSCode
    apt-get update && \
    apt-get install libssl1.0-dev node-gyp nodejs-dev nodejs=8.10* npm -y && \
    pip install python-language-server autopep8 flake8 && \
    # # #Install vscode from workspaces
    chmod +x /var/opt/workspaces/vscode/install && \
    /var/opt/workspaces/vscode/install && \
    #Fix permissions so notebooks start
    chown -R ubuntu:ubuntu /home/ubuntu/.local/ && \
    #clean up
    conda clean -afy && \
    apt-get clean && \
    apt-get autoremove -y && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    # Provide Sudo in container
    echo "ubuntu  ALL=NOPASSWD: ALL" >> /etc/sudoers



# # Set Up Domino Workspaces    
# RUN \
#     apt-get update -y && apt-get install gdebi -y && \
#     # Install Jupyter Notebooks from workspaces
#     chmod +x /var/opt/workspaces/jupyter/install && \
#     /var/opt/workspaces/jupyter/install && \
#     ln -s /opt/microsoft/mlserver/9.3.0/runtime/python/bin/jupyter /usr/local/bin/jupyter && \
#     # Install Rstudio from workspaces
#     chmod +x /var/opt/workspaces/rstudio/install && \
#     /var/opt/workspaces/rstudio/install && \
#     # Add update .Rprofile with Domino customizations
#     mv /var/opt/workspaces/rstudio/.Rprofile /home/ubuntu/.Rprofile && \
#     chown ubuntu:ubuntu /home/ubuntu/.Rprofile && \
#     # Install Jupyterlab from workspaces
#     chmod +x /var/opt/workspaces/Jupyterlab/install && \
#     /var/opt/workspaces/Jupyterlab/install && \
#     ln -s /opt/microsoft/mlserver/9.3.0/runtime/python/bin/jupyter-lab /usr/local/bin/jupyter-lab && \
# # Append dell root authority certificate to python certificates
#     cat /etc/ssl/certs/dellcert.pem >> /opt/microsoft/mlserver/9.3.0/runtime/python/lib/python3.5/site-packages/certifi/cacert.pem && \
# # Ensure user permissions on certain folders
#     chown -R ubuntu:ubuntu /home/ubuntu/ && chown -R ubuntu:ubuntu /opt && chown -R ubuntu:ubuntu /tmp && \
# # Layer Cleanup
#     apt-get clean && apt-get autoclean && apt-get autoremove && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*



# # New additions
# RUN \
#     chown -R ubuntu:ubuntu /opt/* && \
#     conda install -c conda-forge jupyter_contrib_nbextensions jupyter_nbextensions_configurator cookiecutter && \
#     jupyter nbextensions_configurator enable --user && \
#     conda install -c conda-forge pyomo pyomo.extras glpk
        

# ENV \
# # Paths for Python and R
#     PATH="/opt/microsoft/mlserver/9.3.0/libraries/PythonServer:/opt/microsoft/mlserver/9.3.0/runtime/python/bin:/opt/microsoft/mlserver/9.3.0/libraries/RServer:/opt/microsoft/mlserver/9.3.0/runtime/R/bin:$PATH"
    
# # Install some user-requested Python Packages
# RUN \
#     apt-get update -y && apt-get install xvfb -y && \
#     conda install -c conda-forge flatten_json pyodbc textblob pygithub setuptools selenium && \
#     pip install flashtext Guess_Language Langid polyglot && \
#     pip install -U spacy && \
#     python -m spacy download en && \
# # Install Chrome, Chrome-Driver for Selenium
#     cd /tmp && apt-get install -y unzip xvfb libxi6 libgconf-2-4 && \
#     echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google-chrome.list && \
#     wget https://dl.google.com/linux/linux_signing_key.pub && \
#     apt-key add linux_signing_key.pub && \
#     apt-get update && apt-get install -y google-chrome-stable && \
#     mkdir -p /opt/chrome && cd /opt/chrome && \
#     wget https://chromedriver.storage.googleapis.com/2.41/chromedriver_linux64.zip && \
#     unzip chromedriver_linux64.zip && \
#     chmod +x chromedriver && \
#     wget https://selenium-release.storage.googleapis.com/3.13/selenium-server-standalone-3.13.0.jar && \
#     rm /opt/chrome/*.zip && chown -R ubuntu:ubuntu /opt && \
# # Layer Cleanup
#     apt-get clean && apt-get autoclean && apt-get autoremove && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Set user for container
USER ubuntu

