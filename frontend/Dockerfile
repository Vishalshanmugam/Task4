FROM node:20-alpine AS build
WORKDIR /app

# Copy package files for dependency install
COPY package*.json vite.config.ts tsconfig.json ./

# Install dependencies (dev + prod)
RUN npm install

# Copy source code and public assets
COPY index.html .
COPY src ./src
COPY public ./public

# Build production bundle
RUN npx vite build

FROM nginx:alpine
WORKDIR /usr/share/nginx/html

# Remove default Nginx files
RUN rm -rf ./*

# Copy built files from build stage
COPY --from=build /app/dist .

# Expose frontend port
EXPOSE 80

# Run Nginx
CMD ["nginx", "-g", "daemon off;"]
