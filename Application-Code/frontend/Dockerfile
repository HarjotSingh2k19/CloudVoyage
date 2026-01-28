# ==========================================
# STAGE 1: The Build Stage (Using Node.js)
# We name this stage "builder" so we can refer to it later.
# ==========================================
FROM node:18-alpine as builder

WORKDIR /app

# Copy dependency definitions
COPY package*.json ./

# Install dependencies required to BUILD the React app
RUN npm install

# Copy source code
COPY . .

# 1. Accept the argument from docker-compose
ARG REACT_APP_BACKEND_URL
# 2. Set it as an environment variable so 'npm run build' can see it
ENV REACT_APP_BACKEND_URL=$REACT_APP_BACKEND_URL

# This tells Node 18 to allow the old Webpack hashing logic
ENV NODE_OPTIONS=--openssl-legacy-provider

# Build the app. This creates a "build" folder with index.html and static assets.
RUN npm run build



# ==========================================
# STAGE 2: The Production Stage (Using Nginx)
# We start fresh with a new, empty image.
# ==========================================
FROM nginx:alpine

# The Magic Step: Copy ONLY the "build" folder from the "builder" stage above.
# We place it into Nginx's default public folder.
COPY --from=builder /app/build /usr/share/nginx/html

# Expose Port 80 (Standard Web Port)
EXPOSE 80

# Start Nginx in the foreground so the container keeps running
CMD ["nginx", "-g", "daemon off;"]