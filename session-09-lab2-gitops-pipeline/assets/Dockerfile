# The image is built on top of one that has node preinstalled
FROM node:12

# Create app directory
WORKDIR /usr/src/app

# Copy all files into the container
COPY . .

# Install dependencies
RUN npm install

# Open appropriate port
EXPOSE 8181

# Start the application
CMD [ "node", "server.js" ]
