FROM node:16-alpine AS builder

WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build


FROM nginx:1.25-alpine
RUN adduser -D -s /sbin/nologin nonroot && \
    mkdir -p /tmp/nginx && \
    sed -i 's|pid .*;|pid /tmp/nginx/nginx.pid;|' /etc/nginx/nginx.conf && \
    chown -R nonroot /var/cache/nginx /var/log/nginx /tmp/nginx

COPY nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=builder /app/dist/index.html /usr/share/nginx/html/index.html
COPY --from=builder /app/dist /usr/share/nginx/html

USER nonroot
EXPOSE 80

HEALTHCHECK --interval=20s --timeout=3s --start-period=5s --retries=3 \
  CMD wget -qO- http://127.0.0.1/ || exit 1
