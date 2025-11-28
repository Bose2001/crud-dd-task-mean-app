# Use Node to build Angular app
FROM node:18-alpine AS build

WORKDIR /usr/src/app

# Copy package files and install dependencies
COPY package.json package-lock.json ./
RUN npm ci

# Copy all files and build Angular app
COPY . .
RUN npm run build --prod

# Use Nginx to serve Angular app
FROM nginx:alpine
COPY --from=build /usr/src/app/dist/<YOUR_FRONTEND_FOLDER_NAME> /usr/share/nginx/html

# Expose frontend port
EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]

