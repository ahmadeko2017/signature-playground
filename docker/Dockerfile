FROM nginx:1.27-alpine

# Install OpenSSL untuk generate sertifikat self-signed
RUN apk add --no-cache openssl

# Buat folder sertifikat dan generate self-signed cert saat build
RUN mkdir -p /etc/nginx/certs && \
    openssl req -x509 -newkey rsa:2048 -nodes \
      -keyout /etc/nginx/certs/sign.play-key.pem \
      -out /etc/nginx/certs/sign.play.pem \
      -subj "/CN=sign.play" \
      -addext "subjectAltName=DNS:sign.play,IP:127.0.0.1" \
      -days 365

# Copy konfigurasi nginx custom
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Salin file web statis
COPY ./dist /usr/share/nginx/html

EXPOSE 443
EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]