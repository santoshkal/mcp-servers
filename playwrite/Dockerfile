# Generated by https://smithery.ai. See: https://smithery.ai/docs/config#dockerfile
# Use Node.js image for building the project
FROM cgr.dev/chainguard/node:latest-dev AS builder

# Set the working directory
WORKDIR /app
USER root
# Copy package.json and package-lock.json
COPY package.json package-lock.json ./
RUN apk update && apk add chromium
# Install dependencies without running scripts to prevent automatic build
RUN npm install --ignore-scripts

# Copy the entire source directory
COPY src ./src
COPY tsconfig.json ./

# Build the project
RUN npm run build

# Use a minimal Node.js image for running the project
FROM cgr.dev/chainguard/node:latest AS release

# Set the working directory
WORKDIR /app

# Copy the built files from the builder stage
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/package.json ./package.json
COPY --from=builder /app/package-lock.json ./package-lock.json
COPY --from=builder /usr/bin/chromium /usr/bin/chromium

# Install production dependencies
RUN npm ci --ignore-scripts --omit=dev

# Set the command to run the server
ENTRYPOINT ["node", "dist/index.js"]
