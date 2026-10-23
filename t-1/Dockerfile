FROM node:22-alpine

WORKDIR /app

# Copy manifests first so the dependency layer is cached between builds
COPY package*.json ./
RUN npm ci --omit=dev

COPY src ./src

ENV NODE_ENV=production
ENV PORT=3000
EXPOSE 3000

# Run as the non-root user that ships with the node image
USER node

CMD ["node", "src/server.js"]
