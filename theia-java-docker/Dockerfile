ARG NODE_VERSION=10
FROM ibmcom/ibmjava:8-sdk as java-base

FROM node:$NODE_VERSION
COPY --from=java-base /opt/ibm/java /opt/ibm/java

ENV JAVA_HOME=/opt/ibm/java/jre \
    PATH=/opt/ibm/java/jre/bin:/opt/ibm/java/bin/:$PATH

RUN apt-get update && \
  apt-get install -y curl apt-transport-https maven gradle && \
  curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - && \
  echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list && \
  apt-get update && apt-get install -y yarn && \
  rm -rf /var/lib/apt/lists/*
# See : https://github.com/theia-ide/theia-apps/issues/34
RUN adduser --disabled-password --gecos '' user
RUN chmod g+rw /home && \
    mkdir -p /home/project && \
    chown -R user:user /home/user && \
    chown -R user:user /home/project;

RUN apt-get update && apt-get install -y sudo
RUN echo 'user ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

# set npm global

RUN mkdir /home/user/.npm-global
RUN chown -R user:user /home/user/.npm-global
ENV NPM_CONFIG_PREFIX /home/user/.npm-global 
RUN echo 'export PATH=~/.npm-global/bin:$PATH' >> /home/user/.profile

WORKDIR /home/user
USER user

# RUN source /home/user/.profile

ARG version=latest
ADD $version.package.json ./package.json
ARG GITHUB_TOKEN
RUN yarn --cache-folder ./ycache && rm -rf ./ycache
RUN yarn theia build
EXPOSE 3000
ENV SHELL /bin/bash
ENTRYPOINT [ "yarn", "theia", "start", "/home/project", "--hostname=0.0.0.0" ]
