# Use the official Node.js image as the base image
FROM node:19-alpine

# 修改源, 以设置为aliyun为例
echo -e 'https://mirrors.aliyun.com/alpine/v3.7/main/\nhttps://mirrors.aliyun.com/alpine/v3.7/community/' > /etc/apk/repositories

# dockerfile 中修改源,并安装git命令(还需要其他命令,自行添加)
RUN echo -e 'https://mirrors.aliyun.com/alpine/v3.7/main/\nhttps://mirrors.aliyun.com/alpine/v3.7/community/' > /etc/apk/repositories && \
    apk update && \
    apk upgrade && \
    apk add --no-cache git

ARG NODE_ENV

ENV NODE_ENV=$NODE_ENV

# Needed for the wait-for-db script
RUN apk add --no-cache netcat-openbsd

# Set the working directory
WORKDIR /next

# Copy package.json and package-lock.json to the working directory
COPY package*.json ./

# Install dependencies
RUN npm ci

# Copy the wait-for-db.sh script
COPY wait-for-db.sh /usr/local/bin/wait-for-db.sh
RUN chmod +x /usr/local/bin/wait-for-db.sh

# Copy the rest of the application code
COPY . .
COPY entrypoint.sh /

# Ensure correct line endings after these files are edited by windows
RUN apk add --no-cache dos2unix netcat-openbsd \
    && dos2unix /entrypoint.sh


# Expose the port the app will run on
EXPOSE 3000

ENTRYPOINT ["sh", "/entrypoint.sh"]

# Start the application
CMD ["npm", "run", "dev"]
