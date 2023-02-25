ARG UID=101
ARG GID=101

FROM node:16.16.0-buster-slim AS builder

ARG commitHash
ENV DOCKER_COMMIT_HASH=${commitHash}
ENV CYPRESS_INSTALL_BINARY=0

WORKDIR /build
COPY . .
RUN apt-get update
RUN apt-get install -y build-essential rsync
RUN cp mempool-frontend-config.sample.json mempool-frontend-config.json
RUN npm install --omit=dev --omit=optional

RUN npm run build

FROM nginx:1.17.8-alpine
ARG UID
ARG GID

WORKDIR /patch

COPY --from=builder /build/entrypoint.sh .
COPY --from=builder /build/wait-for .
COPY --from=builder /build/dist/mempool /var/www/mempool
COPY --from=builder /build/nginx.conf /etc/nginx/
COPY --from=builder /build/nginx-mempool.conf /etc/nginx/conf.d/

RUN chmod +x /patch/entrypoint.sh
RUN chmod +x /patch/wait-for

<<<<<<< HEAD
RUN chown -R $UID:$GID /patch && chmod -R 755 /patch && \
        chown -R $UID:$GID /var/cache/nginx && \
        chown -R $UID:$GID /var/log/nginx && \
        chown -R $UID:$GID /etc/nginx/nginx.conf && \
        chown -R $UID:$GID /etc/nginx/conf.d
=======
RUN chown -R 1000:1000 /patch && chmod -R 755 /patch && \
        chown -R 1000:1000 /var/cache/nginx && \
        chown -R 1000:1000 /var/log/nginx && \
        chown -R 1000:1000 /etc/nginx/nginx.conf && \
        chown -R 1000:1000 /etc/nginx/conf.d && \
        chown -R 1000:1000 /var/www/mempool

>>>>>>> b6c7c02a2d0d6427c876eabb6fbd6e54b1c49487
RUN touch /var/run/nginx.pid && \
        chown -R $UID:$GID /var/run/nginx.pid

RUN deluser nginx \
    && addgroup -g $GID -S nginx \
    && adduser -S -D -H -u $UID -h /var/cache/nginx -s /sbin/nologin -G nginx -g nginx nginx

USER $UID

ENTRYPOINT ["/patch/entrypoint.sh"]
CMD ["nginx", "-g", "daemon off;"]
