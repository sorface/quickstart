FROM ubuntu as source_code

WORKDIR /source

RUN apt-get update && \
    apt-get install -y git && \
    git clone https://github.com/sorface/passport.git

FROM node:lts as build

ARG REACT_APP_BACKEND_URL
ARG REACT_APP_WS_URL
ARG REACT_APP_VERSION

ENV REACT_APP_BACKEND_URL ${REACT_APP_BACKEND_URL}
ENV REACT_APP_WS_URL ${REACT_APP_WS_URL}
ENV REACT_APP_VERSION ${REACT_APP_VERSION}

WORKDIR /app

COPY --from=source_code /source/passport/client/ .

RUN npm ci &&  \
    npm run build --if-present

FROM nginx:1.23.1-alpine

WORKDIR /usr/share/nginx/html

RUN rm /etc/nginx/conf.d/default.conf

COPY nginx.conf /etc/nginx/templates/nginx.conf.template
COPY mime.conf /etc/nginx/mime.types

COPY --from=build /app/build/ .

RUN ls -a

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]