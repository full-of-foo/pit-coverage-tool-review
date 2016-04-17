FROM buildpack-deps:jessie

### Linux deps
RUN set -ex \
  && apt-get update \
  && apt-get install -y python-software-properties \
  && add-apt-repository ppa:openjdk-r/ppa \
  && apt-get install -y openjdk-7-jdk \
  && rm -rf /var/lib/apt/lists/*
ENV JAVA_HOME /usr/lib/jvm/java-7-openjdk-amd64


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

## Volumes
VOLUME $WORKDIR
VOLUME /root/.m2

## Source
ADD . $WORKDIR

CMD ["mvn"]
