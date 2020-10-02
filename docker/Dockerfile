# -----
# For dev environment
# -----
ARG NODE_VERSION=12

FROM docker.registry.tmm-software.net/docker/node:${NODE_VERSION} as node

ARG NODE_VERSION

ENV \
    NODE_ENV=development \
    NODE_USER=node

COPY . /srv/new-app-web/
COPY ./docker/node/rootfs /
WORKDIR /srv/new-app-web

RUN set -eux &&\
#    # Set the yarn default cache location
#    yarn config set cache-folder /tmp/.yarn-root-cache &&\
    # Install application dependencies
    yarn install --non-interactive --no-progress &&\
    # Build production application
    yarn build &&\
    # Fix permissions for the defined node user
    chown -R "$NODE_USER:$NODE_USER" /srv/new-app-web/build &&\
    chown -R "$NODE_USER:$NODE_USER" /srv/new-app-web/node_modules

ENTRYPOINT [ "/usr/local/sbin/dumb-init", "--", "/docker-entrypoint" ]
CMD [ "yarn", "start" ]

# -----
# For production environment
# -----
FROM nginx:stable-alpine as nginx

ENV \
    API_ENDPOINT="http://localhost:80" \
    WS_ENDPOINT="http://localhost:81"

COPY --from=node /srv/new-app-web/build /srv/new-app-web/build
COPY ./docker/nginx/rootfs /
WORKDIR /srv/new-app-web

RUN set -eux \
    # Prepare system
    && apk add --no-cache --no-progress python2 \
    # Configure nginx
    && rm /etc/nginx/conf.d/default.conf \
    # Clean
    && rm -f -r \
           /root/.npm \
           /tmp/*

ENTRYPOINT [ "/docker-entrypoint" ]
CMD [ "nginx", "-g", "daemon off;" ]
