1. docker build .
Build the image after we set the docker config
Example for node.js app:
```Docker
FROM node:14

WORKDIR /app

COPY package.json .

RUN npm install

COPY . .

EXPOSE 3000

CMD [ "node", "app.mjs" ]
```

2. docker run -p 3000:3000 b275b9b05010 [folder_name]
*folder_name*  if we need it, because we build `.` we don't neen it to add folder_name
*3000:3000* open port from image 
*b275b9b05010* id image.  you can copy the first 10 symbols from the id (b275b9b05010af5fd9b1c2530fca9b59b13932ec868e6e2e26db933c07b59d86). Also you can fiind in the Docker Dekstop. 

4. docker ps
Display your running image

5. docker stop vigilant_vaughan 
*vigilant_vaughan* you can find the name in the running images (last collumn *docker ps*)
It can take few seconds untill (wait a bit

