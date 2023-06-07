# Image
FROM nginx:1.25-alpine

# Add nginx configurations
COPY ./nginx.conf /etc/nginx/nginx.conf
COPY ./snippets /etc/nginx/snippets
COPY ./sites-internal /etc/nginx/sites-internal
COPY ./sites-enabled /etc/nginx/sites-enabled
