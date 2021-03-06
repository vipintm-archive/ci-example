# # Build command
# #     docker build -t test-bare-metal-env:latest .
#
# # Run command
# #     docker run --network host -it test-bare-metal-env:latest




FROM ubuntu:16.04

ENV DEBIAN_FRONTEND=noninteractive

## Needed test packages so newer Fast Model versions work (11.10 and up)
RUN apt-get update -y
RUN apt-get install -y software-properties-common
RUN add-apt-repository -y ppa:ubuntu-toolchain-r/test

RUN apt-get update -y

### System Functionality ###
RUN apt-get install -y apt-utils iputils-ping python2.7
RUN apt-get install -y python-pip

## Fast Models dependencies
RUN apt-get install -y --fix-missing \
	lsb-core     \
	libxext6     \
	libsm6       \
	libxcursor1  \
	libxft2      \
	libxrandr2   \
	libxt6       \
	libxinerama1

## Fast Model newer versions work, update GCC
RUN apt-get install -y gcc-7 g++-7
RUN update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-7 80 --slave /usr/bin/g++ g++ /usr/bin/g++-7


## Unity Junit conversion dependencies
RUN pip install pyparsing junit-xml


### Tool version selection
ARG FM_INSTALL=FastModels_11-10-022_Linux64
ARG FM_DIR=FastModelsTools_11.10

### Install Fast Models
# # Copy Fast Model install file from same directory as this Dockerfile
COPY $FM_INSTALL.tgz /home/

# # install Fast Models, delete artifacts
RUN tar xvzf /home/$FM_INSTALL.tgz            &&\
		cd $FM_INSTALL                &&\
		./setup.sh --i-accept-the-license-agreement --basepath /arm-tools/     &&\
		rm /home/$FM_INSTALL.tgz      &&\
		rm -r /$FM_INSTALL/

# # add setup to /init.sh
RUN echo "set +e; . /arm-tools/$FM_DIR/source_all.sh" >> /arm-tools/init.sh

# # add init.sh to bashrc
RUN echo "source /arm-tools/init.sh"


### Add FVP
ARG FVP_TAR=FVP_MPS2_Cortex-M33_11.10_22.tgz
ADD $FVP_TAR /arm-tools/
RUN /arm-tools/FVP_MPS2_Cortex-M33.sh --i-agree-to-the-contained-eula --destination /arm-tools/Cortex-M33-FVP --no-interactive

### Add unity2junit result conversion file, make output directory for test results
ADD unity_to_junit.py /home/
RUN mkdir /home/output

### Set License
ENV ARMLMD_LICENSE_FILE=7010@localhost
