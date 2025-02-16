FROM nginx:1.23.1-alpine

WORKDIR /usr/share/nginx/html

RUN rm /etc/nginx/conf.d/default.conf

COPY nginx.conf /etc/nginx/templates/nginx.conf.template
COPY mime.conf /etc/nginx/mime.types

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]