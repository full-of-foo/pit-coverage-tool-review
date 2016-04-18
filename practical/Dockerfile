FROM ubuntu:14.04
MAINTAINER Anthony Troy

### Linux deps
ENV DEBIAN_FRONTEND noninteractive
RUN set -ex \
  && sed -i 's/# \(.*multiverse$\)/\1/g' /etc/apt/sources.list \
  && apt-get update \
  && apt-get -y --force-yes install build-essential software-properties-common curl

## Java dep
RUN set -ex \
  && echo oracle-java8-installer shared/accepted-oracle-license-v1-1 select true | debconf-set-selections \
  && add-apt-repository -y ppa:webupd8team/java \
  && apt-get update \
  && apt-get -y --force-yes install oracle-java8-installer \
  && rm -rf /var/lib/apt/lists/*
ENV JAVA_HOME /usr/lib/jvm/java-8-oracle


### Maven dep
ENV MAVEN_VERSION 3.3.9
RUN mkdir -p /usr/share/maven \
  && curl -fsSL http://apache.osuosl.org/maven/maven-3/$MAVEN_VERSION/binaries/apache-maven-$MAVEN_VERSION-bin.tar.gz \
    | tar -xzC /usr/share/maven --strip-components=1 \
  && ln -s /usr/share/maven/bin/mvn /usr/bin/mvn
ENV MAVEN_HOME /usr/share/maven


### App
ENV WORKDIR /usr/src/app
RUN mkdir -p $WORKDIR
WORKDIR $WORKDIR
VOLUME $WORKDIR

## Add Cache-order Source
ADD pit-review/pom.xml $WORKDIR
RUN mvn verify clean --fail-never
ADD pit-review/src $WORKDIR/src

## Compile and run tests
CMD mvn verify
