FROM ubuntu as source_code

WORKDIR /source

ARG GITHUB_BRANCH

RUN apt-get update &&  apt-get install -y git && git clone https://github.com/sorface/frontend.web.id.git -b $GITHUB_BRANCH

FROM node:lts as build

ARG REACT_APP_GATEWAY_URL
ARG REACT_APP_PASSPORT_PREFIX
ARG REACT_APP_BUILD_HASH

ENV REACT_APP_GATEWAY_URL ${REACT_APP_GATEWAY_URL}
ENV REACT_APP_PASSPORT_PREFIX ${REACT_APP_PASSPORT_PREFIX}
ENV REACT_APP_BUILD_HASH ${REACT_APP_BUILD_HASH}

WORKDIR /app

COPY --from=source_code /source/frontend.web.id/ .

RUN npm ci && npm run build --if-present

FROM nginx:1.23.1-alpine

WORKDIR /usr/share/nginx/html

RUN rm /etc/nginx/conf.d/default.conf

COPY nginx.conf /etc/nginx/templates/nginx.conf.template
COPY mime.conf /etc/nginx/mime.types

COPY --from=build /app/build/ .

RUN ls -a

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]