FROM node:18 AS build
WORKDIR /app


COPY . .

FROM nginx:alpine
WORKDIR /usr/share/nginx/html

COPY --from=build /app /usr/share/nginx/html

EXPOSE 80

