FROM node:12-alpine AS build

WORKDIR /app

COPY package.json ./
RUN npm install

COPY . ./

ARG API_ROOT=http://localhost:3100/api
RUN sed -i "s|https://conduit.productionready.io/api|${API_ROOT}|g" src/agent.js \
    && npm run build

FROM nginx:1.25-alpine

COPY --from=build /app/build /usr/share/nginx/html
EXPOSE 80
