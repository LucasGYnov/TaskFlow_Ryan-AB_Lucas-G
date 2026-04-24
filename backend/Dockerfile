FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./

RUN npm ci
COPY . .

FROM node:20-alpine AS production
WORKDIR /app

LABEL org.opencontainers.image.version="v1.0.0"
LABEL org.opencontainers.image.authors="Lucas and Ryan"
LABEL org.opencontainers.image.source="https://github.com/LucasGYnov/TaskFlow_Ryan-AB_Lucas-G"

COPY --from=builder /app/server.js ./
COPY package*.json ./
RUN npm ci --omit=dev

RUN mkdir -p /app/data && chown -R node:node /app/data
USER node 

HEALTHCHECK --interval=30s --timeout=5s --retries=3 CMD wget -qO- http://127.0.0.1:3001/health || exit 1

EXPOSE 3001
CMD [ "node", "server.js" ]