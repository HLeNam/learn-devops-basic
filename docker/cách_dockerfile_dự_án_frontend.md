# Dockerfile Frontend

[Mẫu docker file](https://elroydevops.tech/mau-dockerfile-cac-du-an/)

# Vue js

Tạo Dockerfile

vi Dockerfile

```
## Build stage ##
FROM node:18.18-alpine AS build

WORKDIR /app
COPY . .
RUN npm install
RUN npm run build

## Run stage ##
FROM nginx:alpine

COPY --from=build /app/dist /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

root@ubuntuserver:/data/todolist# docker build -t todolist:v1 .
