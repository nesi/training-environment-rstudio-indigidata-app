FROM rocker/geospatial:latest

# install system dependencies
# TODO: move ldap-utils, libnss-ldapd, libpam-ldapd, nscd, nslcd to base image??
RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y \
        curl \
        dnsutils \
        git \
        jq \
        ldap-utils \
        libnss-ldapd \
        libpam-ldapd \
        less \
        nano \
        nodejs \
        nscd \
        nslcd \
        rsync \
        unzip \
        vim \
        wget \
        zip \
    && rm -rf /var/lib/apt/lists/*

# install kubectl, required for running on the k8s cluster
ARG KUBECTL_VERSION=v1.28.5
RUN curl -LO https://dl.k8s.io/release/$KUBECTL_VERSION/bin/linux/amd64/kubectl \
    && mv kubectl /usr/local/bin/kubectl \
    && chmod +x /usr/local/bin/kubectl

# copy in ood k8s utils
ARG UTILS_HASH=6298fb01f7a7c66a8454e3f0fd74437a32491423
RUN git clone https://github.com/nesi/training-environment-k8s-utils.git /opt/ood-k8s-utils \
    && cd /opt/ood-k8s-utils \
    && git checkout $UTILS_HASH \
    && chmod +x /opt/ood-k8s-utils/files/* \
    && mv /opt/ood-k8s-utils/files/* /bin/ \
    && rm -rf /opt/ood-k8s-utils

# make a dummy module command to avoid warnings from ondemand job_script_content.sh
RUN echo "#!/bin/bash" > /bin/module \
    && chmod +x /bin/module

# install R packages
RUN Rscript -e 'install.packages("BiocManager", repos = "https://cloud.r-project.org")' \
    && Rscript -e 'BiocManager::install("phyloseq")' \
    && Rscript -e 'install.packages("beeswarm")' \
    && Rscript -e 'install.packages("knitr")' \
    && Rscript -e 'install.packages("gplots")' \
    && Rscript -e 'install.packages("dplyr")' \
    && Rscript -e 'install.packages("ggplot2")' \
    && Rscript -e 'install.packages("vegan")' \
    && Rscript -e 'install.packages("tidyverse")' 


# copy data
COPY docker/OTU_table.biom /var/lib/
COPY docker/spiders_df.csv.zip /var/lib/


RUN unzip /var/lib/spiders_df.csv.zip -d /var/lib \
    && rm -f /var/lib/spiders_df.csv.zip \
    && chown -R root:root /var/lib/spiders_df.csv \
    && chmod -R o+rX /var/lib/spiders_df.csv

