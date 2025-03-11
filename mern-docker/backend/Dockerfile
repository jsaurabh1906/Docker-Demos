# set the base image to create the image for react app
FROM node:23-alpine3.20

# RUN addgroup app && adduser -S -G app app
# USER app

WORKDIR /app

COPY package*.json ./

# USER root
# RUN chown -R app:app .
# USER app

RUN npm install

COPY . .

EXPOSE 8000

CMD npm start