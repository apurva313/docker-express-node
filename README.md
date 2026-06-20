

# Dockerizing Node.js + MongoDB Application

## Project Overview

This project is a simple Node.js application built using:

- Node.js
- Express.js
- MongoDB
- Docker
- Docker Compose

The application:

- Accepts user data through a form
- Stores data in MongoDB
- Retrieves users from MongoDB

---

# Project Structure

```text
docker-express-node/
│
├── public/
│   ├── index.html
│   └── style.css
│
├── server.js
├── package.json
├── package-lock.json
├── .env
├── Dockerfile
├── docker-compose.yml
└── README.md
```

---

# Step 1: Create .env File

Create a file named `.env`

```env
PORT=5050

MONGO_USERNAME=admin
MONGO_PASSWORD=qwerty

MONGO_DB=apurva-db

MONGO_URL=mongodb://admin:qwerty@mongodb:27017
```

---

# Step 2: Update server.js

Replace hardcoded values with environment variables.

```javascript
require("dotenv").config();

const express = require("express");
const app = express();

const MongoClient = require("mongodb").MongoClient;

const PORT = process.env.PORT;

const MONGO_URL = process.env.MONGO_URL;

const client = new MongoClient(MONGO_URL);

app.use(express.urlencoded({ extended: true }));
app.use(express.static("public"));

app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

---

# Step 3: Install dotenv

```bash
npm install dotenv
```

---

# Step 4: Create Dockerfile

Create a file named `Dockerfile`

```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 5050

CMD ["node", "server.js"]
```

---

# Step 5: Create docker-compose.yml

```yaml
version: "3.8"

services:
  mongodb:
    image: mongo

    container_name: mongodb

    restart: always

    environment:
      MONGO_INITDB_ROOT_USERNAME: admin
      MONGO_INITDB_ROOT_PASSWORD: qwerty

    ports:
      - "27017:27017"

    volumes:
      - mongo-data:/data/db

  app:
    build: .

    container_name: node-app

    restart: always

    ports:
      - "5050:5050"

    depends_on:
      - mongodb

    env_file:
      - .env

volumes:
  mongo-data:
```

---

# Step 6: Build Docker Image

Navigate to project directory.

```bash
docker build -t docker-testapp .
```

Verify image:

```bash
docker images
```

---

# Step 7: Run MongoDB Container

```bash
docker run -d ^
--name mongodb ^
-p 27017:27017 ^
-e MONGO_INITDB_ROOT_USERNAME=admin ^
-e MONGO_INITDB_ROOT_PASSWORD=qwerty ^
mongo
```

Check:

```bash
docker ps
```

---

# Step 8: Run Node.js Container

```bash
docker run -d ^
--name node-app ^
-p 5050:5050 ^
docker-testapp
```

---

# Step 9: Run Using Docker Compose

Build and start all services:

```bash
docker compose up -d
```

View running containers:

```bash
docker ps
```

---

# Step 10: Check Logs

Application logs:

```bash
docker logs node-app
```

MongoDB logs:

```bash
docker logs mongodb
```

Live logs:

```bash
docker logs -f node-app
```

---

# Step 11: Stop Containers

```bash
docker stop node-app mongodb
```

---

# Step 12: Remove Containers

```bash
docker rm node-app mongodb
```

---

# Step 13: Stop Docker Compose

```bash
docker compose down
```

Remove volumes also:

```bash
docker compose down -v
```

---

# Useful Docker Commands

### List Images

```bash
docker images
```

### List Containers

```bash
docker ps
```

### List All Containers

```bash
docker ps -a
```

### Remove Image

```bash
docker rmi docker-testapp
```

### Enter Container

```bash
docker exec -it node-app sh
```

### Inspect Container

```bash
docker inspect node-app
```

---

# Application Access

| Service  | URL                                            |
| -------- | ---------------------------------------------- |
| Node App | [http://localhost:5050](http://localhost:5050) |
| MongoDB  | localhost:27017                                |

---

# Architecture

```text
Browser
   │
   ▼
Node.js Container
   │
   ▼
MongoDB Container
   │
   ▼
Docker Volume
```


