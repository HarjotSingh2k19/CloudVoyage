# 1. Base Image: Use a lightweight version of Node.js (Alpine Linux)
FROM node:18-alpine

# 2. Work Directory: Set the folder inside the container where your app will live.
WORKDIR /app

# 3. Cache Optimization: Copy ONLY package.json first.
# Docker caches layers. If you change your code but NOT your dependencies, 
# Docker skips the 'npm install' step next time, making builds super fast.
COPY package*.json ./

# 4. Install Dependencies: Downloads all the libraries listed in package.json
RUN npm install

# 5. Copy Code: Now copy the rest of your source code into the container.
COPY . .

# 6. Documentation: Tell Docker this container listens on Port 3500.
EXPOSE 3500

# 7. Startup Command: The command that runs when the container starts.
CMD ["npm", "start"]
