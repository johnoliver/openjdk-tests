# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      https://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# This Dockerfile in thirdparty_containers/payara-mp-tck/dockerfile dir is used to create an image with
# AdoptOpenJDK jdk binary installed. Basic test dependent executions
# are installed during the building process.
#
# Build example: `docker build -t adoptopenjdk-payara-mp-tck .`
#
# This Dockerfile builds image based on adoptopenjdk/openjdk8:latest.
# If you want to build image based on other images, please use
# `--build-arg list` to specify your base image
#
# Build example: `docker build --build-arg IMAGE_NAME=<image_name> --build-arg IMAGE_VERSION=<image_version> -t adoptopenjdk-payara-mp-tck .`

ARG SDK=openjdk8
ARG IMAGE_NAME=adoptopenjdk/$SDK
ARG IMAGE_VERSION=latest

FROM $IMAGE_NAME:$IMAGE_VERSION

# Install test dependent executable files
RUN apt-get update \
	&& apt-get -y install \
	ant \
	apt-transport-https \
	ca-certificates \
	dirmngr \
	curl \
	git \
	make \
	unzip \
	vim \
	wget \
	python3-pip python3-dev \
	&& cd /usr/local/bin \
	&& ln -s /usr/bin/python3 python \
	&& pip3 install --upgrade pip 

WORKDIR /
ENV PAYARA_HOME $WORKDIR
	
# Install Maven
RUN mkdir -p /usr/share/maven \
	&& cd /usr/share/maven \
	&& wget --no-check-certificate --no-cookies https://archive.apache.org/dist/maven/maven-3/3.5.3/binaries/apache-maven-3.5.3-bin.zip \
	&& unzip apache-maven-3.5.3-bin.zip \
	&& rm -rf apache-maven-3.5.3-bin.zip \
	&& ln -s /usr/share/maven/apache-maven-3.5.3/bin/mvn /usr/bin/mvn \
	&& cd /

VOLUME ["/java"]
ENV JAVA_HOME=/java \
    PATH=/java/bin:$PATH \
    JAVA_TOOL_OPTIONS="-Dfile.encoding=UTF8"

COPY ./dockerfile/payara-mp-tck.sh /payara-mp-tck.sh

#Download and unzip Payara full distribution
RUN curl -L -o /tmp/payara-5.182.zip https://search.maven.org/remotecontent?filepath=fish/payara/distributions/payara/5.182/payara-5.182.zip && \
    unzip /tmp/payara-5.182.zip -d / && \
    rm -f /tmp/payara-5.182.zip
 
# Clone the Pyara MicroProfile-TCK-Runners
RUN pwd
RUN git clone https://github.com/payara/MicroProfile-TCK-Runners

ENTRYPOINT ["/bin/bash", "/payara-mp-tck.sh"]
CMD ["--version"]
