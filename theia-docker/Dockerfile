ARG NODE_VERSION=10
FROM node:${NODE_VERSION}-alpine
RUN apk add --no-cache make gcc g++ python
ARG version=latest
WORKDIR /home/user
ADD $version.package.json ./package.json
ARG GITHUB_TOKEN
RUN yarn --pure-lockfile && \
    NODE_OPTIONS="--max_old_space_size=4096" yarn theia build && \
    yarn --production && \
    yarn autoclean --init && \
    echo *.ts >> .yarnclean && \
    echo *.ts.map >> .yarnclean && \
    echo *.spec.* >> .yarnclean && \
    yarn autoclean --force && \
    yarn cache clean

FROM node:${NODE_VERSION}-alpine
RUN apk add --no-cache make gcc g++ python
RUN apk add --no-cache chromium
# See : https://github.com/theia-ide/theia-apps/issues/34
RUN addgroup user && \
    adduser -G user -s /bin/sh -D user;
RUN chmod g+rw /home && \
    mkdir -p /home/project && \
    chown -R user:user /home/user && \
    chown -R user:user /home/project;

# set npm global

RUN mkdir /home/user/.npm-global
RUN chown -R user:user /home/user/.npm-global
ENV NPM_CONFIG_PREFIX /home/user/.npm-global
ENV PATH=$PATH:/home/user/.npm-global/bin/

RUN apk add --no-cache git openssh bash
RUN apk add --no-cache sudo
RUN echo 'user ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

ENV HOME /home/user
WORKDIR /home/user
COPY --from=0 --chown=user:user /home/user /home/user
EXPOSE 3000
ENV SHELL /bin/bash
ENV USE_LOCAL_GIT true
USER user
ENTRYPOINT [ "node", "/home/user/src-gen/backend/main.js", "/home/project", "--hostname=0.0.0.0" ]
