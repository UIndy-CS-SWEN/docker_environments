# syntax=docker/dockerfile:1
FROM ubuntu:21.10

ARG DEBIAN_FRONTEND="noninteractive"

RUN mkdir -p /home/app
WORKDIR /home/app

# replace bourne with bash to be able to source files
RUN rm /bin/sh && ln -s /bin/bash /bin/sh


# update the repository sources list
# and install dependencies
# apt-get install -y curl wget
RUN apt-get update \
    && apt-get install -y \
    curl \
    wget \
    ca-certificates \
    sudo \
    git \
    bzip2 \
    libx11-6 \
    && apt-get -y autoclean
    
    
# install python3.9 (in the system using root)
RUN apt-get install -y python3.9 python3.9-distutils

# install pip (in the system using root)
RUN ln -s /usr/bin/python3.9 /usr/bin/python

# install pip
RUN wget https://bootstrap.pypa.io/get-pip.py \
    && python3.9 get-pip.py \ 
    && python3.9 -m pip install --upgrade pip


# Create a non-root user and switch to it
RUN adduser --disabled-password --gecos '' --shell /bin/bash user \
 && chown -R user:user /home/app
RUN echo "user ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/90-user
USER user

# All users can use /home/user as their home directory
ENV HOME=/home/user
RUN mkdir $HOME/.cache $HOME/.config \
 && chmod -R 777 $HOME

# Adding this path because the package will be installed in this path
ENV PATH "$PATH:/home/user/.local/bin"

# install numpy scipy matplotlib ipython pandas sympy nose
#RUN pip install --user -U numpy scipy scikit-learn matplotlib ipython pandas 

# install nltk
#RUN pip install --user -U nltk

# install anaconda and all the ds and nltk libraries
ENV CONDA_AUTO_UPDATE_CONDA=false \
    PATH=$HOME/miniconda/bin:$PATH
COPY environment.yml /home/app/environment.yml
RUN curl -sLo ~/miniconda.sh https://repo.continuum.io/miniconda/Miniconda3-py39_4.10.3-Linux-x86_64.sh \
 && chmod +x ~/miniconda.sh \
 && ~/miniconda.sh -b -p ~/miniconda \
 && rm ~/miniconda.sh \
 && conda env update -n base -f /home/app/environment.yml \
 && rm /home/app/environment.yml \
 && conda clean -ya


CMD [ "/bin/bash" ]