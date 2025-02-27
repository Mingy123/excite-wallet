FROM node:alpine AS base

FROM base AS dependencies

RUN apk --no-cache add --virtual build-deps build-base python3
WORKDIR /app/
COPY package.json package-lock.json ./
RUN npm ci --include=dev

FROM base AS build

ARG BACKEND_URL=http://localhost:3000
ENV VITE_BACKEND_URL=$BACKEND_URL
WORKDIR /app/
COPY . .
COPY --from=dependencies /app/node_modules ./node_modules
RUN npm run build

FROM busybox:musl AS deploy

WORKDIR /app/
COPY --from=build /app/dist .
COPY httpd.conf /etc/httpd.conf
CMD ["busybox", "httpd", "-f", "-v", "-p", "8080", "-c", "/etc/httpd.conf"]