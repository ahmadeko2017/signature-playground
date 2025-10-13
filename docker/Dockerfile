# =============================================
# === STAGE 1: Build React App (Vite + Node) ==
# =============================================
FROM node:20.11.1-alpine3.19 AS builder

LABEL maintainer="Ahmad Eko Kurniawan <ahmad@example.com>" \
      description="Signature Playground v6 build image (React + Vite)" \
      stage="builder"

# Set working directory
WORKDIR /app

# Optimize cache: copy only package definition first
COPY package*.json ./

# Install dependencies (production only)
RUN npm ci --omit=dev

# Copy all source code (filtered via .dockerignore)
COPY . .

# Build production-ready static files
RUN npm run build


# ==========================================
# === STAGE 2: Hardened Nginx Runtime ======
# ==========================================
FROM nginx:1.27.1-alpine3.19 AS runtime

LABEL maintainer="Ahmad Eko Kurniawan <ahmad@example.com>" \
      description="Signature Playground v6 runtime (Nginx static HTTPS)"

# Fallback: create non-root user safely if not exists
RUN addgroup -S appgroup && adduser -S -G appgroup -u 1001 appuser \
    && mkdir -p /usr/share/nginx/html /etc/nginx/certs /var/cache/nginx \
    && chown -R appuser:appgroup /usr/share/nginx/html /etc/nginx /var/cache/nginx

# Copy Nginx config
COPY docker/nginx.conf /etc/nginx/conf.d/default.conf

# Copy build output from builder stage
COPY --from=builder /app/dist /usr/share/nginx/html

# Switch to non-root user
USER appuser

# Expose only HTTPS port
EXPOSE 443

# Run Nginx in foreground mode
CMD ["nginx", "-g", "daemon off;"]