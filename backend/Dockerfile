# =========================
# Stage 1: Install dependencies
# =========================
FROM node:22-alpine AS deps

WORKDIR /app

COPY package.json package-lock.json ./

# Install only production dependencies
RUN npm ci --omit=dev


# =========================
# Stage 2: Runtime image
# =========================
FROM node:22-alpine

WORKDIR /app

# Fix Alpine/OpenSSL vulnerabilities
RUN apk upgrade --no-cache

# npm/yarn are not required to run the application.
# Removing them also removes vulnerable npm bundled packages
# such as tar, pacote, picomatch, etc.
RUN rm -rf \
    /usr/local/lib/node_modules/npm \
    /usr/local/bin/npm \
    /usr/local/bin/npx \
    /usr/local/lib/node_modules/corepack \
    /usr/local/bin/corepack \
    /opt/yarn* \
    /usr/local/bin/yarn \
    /usr/local/bin/yarnpkg

# Copy production dependencies
COPY --from=deps /app/node_modules ./node_modules

# Copy application source
COPY . .

# Run as non-root user
USER node

EXPOSE 3000

# Run Node directly; npm is not required at runtime
CMD ["node", "src/server.js"]