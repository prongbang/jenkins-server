FROM jenkins/jenkins:2.60.3

ENV ANDROID_HOME /opt/android-sdk-linux
ENV PATH $PATH:$ANDROID_HOME/tools
ENV PATH $PATH:$ANDROID_HOME/platform-tools
ENV PATH $PATH:/opt/gradle-3.0/bin

USER root

COPY ["entrypoint.sh", "/"]

RUN mkdir /var/log/jenkins
RUN mkdir /var/cache/jenkins
RUN chown -R jenkins:jenkins /var/log/jenkins
RUN chown -R jenkins:jenkins /var/cache/jenkins
 
RUN apt-get update && apt-get install -y apt-transport-https
RUN apt-get -q -y install lsof

ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install --assume-yes apt-utils

RUN wget https://dl.google.com/android/android-sdk_r24.4.1-linux.tgz -O /opt/android-sdk.tgz
RUN tar zxvf /opt/android-sdk.tgz -C /opt/
RUN rm /opt/android-sdk.tgz
 
RUN >/etc/profile.d/android.sh
RUN sed -i '$ a\export ANDROID_HOME="/opt/android-sdk-linux"' /etc/profile.d/android.sh
RUN sed -i '$ a\export PATH="$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools:$PATH"' /etc/profile.d/android.sh
RUN . /etc/profile
RUN apt-get install git-core
RUN ( sleep 5 && while [ 1 ]; do sleep 1; echo y; done ) | /opt/android-sdk-linux/tools/android update sdk --no-ui --filter platform-tools,android-24,build-tools-24.0.1,tools,extra-android-support,extra-android-m2repository
RUN chmod -R 755 /opt/android-sdk-linux
RUN dpkg --add-architecture i386

# Cleanup
RUN apt-get clean
RUN rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

USER jenkins
ENV JAVA_OPTS="-Xmx8192m"
	
# List desired Jenkins plugins here
RUN /usr/local/bin/install-plugins.sh git gradle

USER root
RUN apt-get update && \
    apt-get install sudo && \
    chmod 755 /entrypoint.sh

ENTRYPOINT ["/bin/bash","-c","./entrypoint.sh"]