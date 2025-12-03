# ============================

# Stage 1 — Build stage

# ============================

FROM node:20 AS builder

# Metadata (dipakai CI/CD, dokumentasi, auditing)

LABEL org.opencontainers.image.title="auth-service"
LABEL org.opencontainers.image.description="Microservice untuk authentication"
LABEL org.opencontainers.image.version="1.0.0"
LABEL [maintainer="team-backend@example.com](mailto:maintainer=%22team-backend@example.com)"

# Set working directory

WORKDIR /app

# Copy only files yang dibutuhkan untuk install dependencies

COPY package*.json ./

# Install dependencies (menggunakan clean install)

RUN npm ci

# Copy seluruh source code

COPY . .

# Build jika perlu

RUN npm run build

# ============================

# Stage 2 — Runtime stage

# ============================

FROM node:20-slim AS runner

# Set environment variable default

ENV NODE_ENV=production

WORKDIR /app

# Copy hanya file build dan node_modules dari builder

COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/dist ./dist

# Expose port

EXPOSE 3000

# Command yang dipakai container saat jalan

CMD ["node", "dist/server.js"]
