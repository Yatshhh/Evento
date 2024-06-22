# Fetching the latest node image 
FROM node:alpine AS development

# Setting up the work directory
WORKDIR /react-app

# Installing dependencies
COPY package*.json ./

RUN npm install

# Copying all the files in our project
COPY . .

#expose port
EXPOSE 8000

# Starting our application
CMD ["npm","start"]
