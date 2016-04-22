FROM ubuntu:14.04
MAINTAINER Anthony Troy

### Linux deps
ENV DEBIAN_FRONTEND noninteractive
RUN set -ex \
  && sed -i 's/# \(.*multiverse$\)/\1/g' /etc/apt/sources.list \
  && apt-get update \
  && apt-get -y --force-yes install build-essential software-properties-common curl unzip


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

RUN set -ex \
  && curl -O https://raw.githubusercontent.com/git/git/master/contrib/completion/git-completion.bash \
  && curl -O https://raw.githubusercontent.com/juven/maven-bash-completion/master/bash_completion.bash \
  && mv git-completion.bash ~/.git_completion.bash \
  && mv bash_completion.bash ~/.mvn_bash_completion.bash \
  && echo "source ~/.git_completion.bash" >> ~/.bashrc \
  && echo "source ~/.mvn_bash_completion.bash" >> ~/.bashrc


### Ant dep
ENV ANT_VERSION 1.9.4
RUN set -ex \
  && cd \
  && wget -q http://archive.apache.org/dist/ant/binaries/apache-ant-${ANT_VERSION}-bin.tar.gz \
  && tar -xzf apache-ant-${ANT_VERSION}-bin.tar.gz \
  && mv apache-ant-${ANT_VERSION} /opt/ant \
  && rm apache-ant-${ANT_VERSION}-bin.tar.gz
ENV ANT_HOME /opt/ant
ENV PATH ${PATH}:/opt/ant/bin


### Gradle dep
ENV GRADLE_VERSION 2.7
ENV GRADLE_SHA cde43b90945b5304c43ee36e58aab4cc6fb3a3d5f9bd9449bb1709a68371cb06
RUN set -ex \
 && cd /usr/lib \
 && curl -fl https://downloads.gradle.org/distributions/gradle-${GRADLE_VERSION}-bin.zip -o gradle-bin.zip \
 && echo "$GRADLE_SHA gradle-bin.zip" | sha256sum -c - \
 && unzip "gradle-bin.zip" \
 && ln -s "/usr/lib/gradle-${GRADLE_VERSION}/bin/gradle" /usr/bin/gradle \
 && rm "gradle-bin.zip"
ENV GRADLE_HOME /usr/lib/gradle
ENV PATH $PATH:$GRADLE_HOME/bin


### App
ENV WORKDIR /usr/src/app
RUN mkdir -p $WORKDIR/maven-pit-review
RUN mkdir -p $WORKDIR/ant-pit-review
RUN mkdir -p $WORKDIR/gradle-pit-review
WORKDIR $WORKDIR
VOLUME $WORKDIR

## Add Maven cache-order source
ADD maven-pit-review/pom.xml $WORKDIR/maven-pit-review
RUN cd maven-pit-review && mvn verify clean --fail-never
ADD naive-app/src $WORKDIR/maven-pit-review/src/main/java
ADD naive-app/test $WORKDIR/maven-pit-review/src/test/java
## Add Ant source
ADD ant-pit-review $WORKDIR/ant-pit-review/
ADD naive-app $WORKDIR/ant-pit-review/
## Add Gradle source
ADD gradle-pit-review $WORKDIR/gradle-pit-review/
ADD naive-app/src $WORKDIR/gradle-pit-review/src/main/java
ADD naive-app/test $WORKDIR/gradle-pit-review/src/test/java

## Compile then run Ant and Maven tests
RUN echo "Running naive-app PIT tests under Maven..."
RUN cd maven-pit-review && mvn verify
RUN echo "Running naive-app PIT tests under Ant..."
RUN cd ant-pit-review && ant pit
RUN echo "Running naive-app PIT tests under Gradle..."
RUN cd gradle-pit-review && gradle pitest
