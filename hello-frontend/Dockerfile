# Stage 1: Build the application
FROM node:18 AS build
WORKDIR /app

# Copy package.json and package-lock.json (if using npm for frontend tools)
# COPY package*.json ./

# Install a simple HTTP server (if needed)
# RUN npm install -g http-server

# Copy the frontend files
COPY . .

# Stage 2: Create the runtime image
FROM nginx:alpine
WORKDIR /usr/share/nginx/html

# Copy the frontend files from the build stage
COPY --from=build /app /usr/share/nginx/html

# Expose port 80 (default for Nginx)
EXPOSE 80

# Nginx starts automatically, so no need for a CMD