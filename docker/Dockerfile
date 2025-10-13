FROM nginx:1.27-alpine

# Copy sertifikat hasil generate pipeline
COPY certs/sign.play.pem /etc/nginx/certs/sign.play.pem
COPY certs/sign.play-key.pem /etc/nginx/certs/sign.play-key.pem

# Copy konfigurasi Nginx
COPY nginx.conf /etc/nginx/conf.d/default.conf

COPY ./dist /usr/share/nginx/html

EXPOSE 443
CMD ["nginx", "-g", "daemon off;"]