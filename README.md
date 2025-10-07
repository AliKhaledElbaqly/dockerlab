![Docker](https://img.shields.io/badge/Container-Docker-blue?logo=docker&logoColor=white)
![Flask](https://img.shields.io/badge/Framework-Flask-black?logo=flask)
![Redis](https://img.shields.io/badge/Database-Redis-red?logo=redis)
![Python](https://img.shields.io/badge/Language-Python-yellow?logo=python)
![License](https://img.shields.io/badge/License-MIT-green)
![Linux](https://img.shields.io/badge/OS-Linux-lightgrey?logo=linux)
![Status](https://img.shields.io/badge/Status-Learning%20Project-orange)


<div align="center">
  
 [![Typing SVG](https://readme-typing-svg.demolab.com?font=Fira+Code&size=25&pause=1000&color=00BFFF&center=true&vCenter=true&width=600&lines=welcome+to+my+learning+base!;Always+learning+new+Concepts!;Always+learning+new+Tools!)](https://git.io/typing-svg)

</div>


#  Docker Lab — beginneer Project1
### [Project 1 Explains the difference between destinitions inside  two differend Features ](./project1)


| Feature                  | Nginx                                              | Apache (httpd)                                |
|--------------------------|---------------------------------------------------|-----------------------------------------------|
| Default web root (HTML path) | /usr/share/nginx/html                              | /usr/local/apache2/htdocs                       |
| Main config file         | /etc/nginx/nginx.conf                              | /usr/local/apache2/conf/httpd.conf             |
| Primary use             | Reverse proxy, load balancer, high-performance static server | Classic web server, good for dynamic apps (PHP, etc.) |
| Performance style        | Event-driven (handles many requests efficiently) | Process/thread-based (spawns worker per request/thread) |
| Container image name     | nginx or nginx:stable                              | httpd                                          |
| Default port inside container | 80                                                | 80                                             |

---

### Hands on lab
 
🟩 Nginx

```
docker run -d --name nginx-web -p 8080:80 -v $(pwd)/website:/usr/share/nginx/html nginx:stable
```
🟥 Apache
```
docker run -d --name apache-web -p 8081:80 -v $(pwd)/website:/usr/local/apache2/htdocs httpd
```

Test

    • http://localhost:8080 → served by Nginx
    • http://localhost:8081 → served by Apache


---

#  Docker Lab — beginneer Project2
### [Project2 Continues With project1.](./project2)
- Clone a repo html page from github
- package it using Dockerfile, add the html page directory destinition to the host service.
- Use command `docker build .`
- Clean up with `docker system prune` 

---

#  Docker Lab — Intermediate Project3
[Project3! Flask + Redis database Counter App ](./project3)
###  

<p align="center">
  A small learning lab where I containerized a Flask web app connected to Redis using Docker Compose.<br>
  🧠 Built as part of my DevOps learning journey to practice real container based setups.
</p>

---

##  Preview
> Simple Flask web app that counts page visits using Redis

Hello From Project3, I have been seen 12 times!!!


---

##  Tech Stack

| Component | Description |
|------------|-------------|
| 🐍 **Flask** | Python web framework used for the frontend |
| 🧠 **Redis** | In memory key value database to store the counter |
| 🐳 **Docker Compose** | Manages both containers and connects them through a shared network |
| 🧾 **Alpine Image** | Lightweight base for smaller container footprint |
| 🧰 **Linux Ubuntu** | Development environment |

---

## Project Structure

```
Docker_lab/ 
└── project3/ 
├── app.py 
├── Dockerfile 
├── docker-compose.yml 
└── prereq.txt 
```

---

##  Features

✅ Multi-container setup (Flask + Redis)  
✅ Auto networking through Docker Compose  
✅ Simple connection retry logic for Redis  
✅ Lightweight Alpine image for Python  
✅ Hands on practice for DevOps beginners  

---

##  app.py Stage 1 The Default Port! 

```python
import time
import redis
from flask import Flask

app = Flask(__name__)
cache = redis.Redis(host='db', port=6379)  # Service name from docker-compose , redis port aswell

def get_hit_count():
    retries = 5
    while retries > 0:
        try:
            return cache.incr('hits')
        except redis.exceptions.ConnectionError:
            retries -= 1
            print(f"Redis not ready, retrying... ({retries} left)")
            time.sleep(1)
    return 0

@app.route('/')
def hello():
    count = get_hit_count()
    return f'Hello From Project3, I have been seen {count} times.\n'
```

## ▶️ How to Run

### 1. Build and start the containers

```shell
docker-compose up --build -d
```

### 2. Check containers

```shell
docker ps
```

### 3. Results 

```shell
project3-frontend-1   ...   0.0.0.0:9000->5000/tcp
project3-db-1          ...   6379/tcp
```

### 4. Test by Open in browser

```
http://localhost:9000
```

### 5. Clean up

```
docker compose down --rmi all 
# -v in case to remove volumes as well
```

## Troubleshooting

| Issue                                 | Cause                        | Solution                             |
| ------------------------------------- | ---------------------------- | ------------------------------------ |
| `Internal Server Error`               | Flask can’t connect to Redis | Ensure `host='db'` in app.py         |
| `manifest for redis:apline not found` | Typo in image name           | Use `redis:alpine`                   |
| Redis connection refused              | Redis starts slowly          | Retry logic handles it automatically |     |
| Logs inspections              | Aren't sure and need to know          | docker logs command  |     |

---

## Stage 2 Improvements
- Added persistent port (5001) in Flask app with improved retry.

- Updated docker-compose.yml to mount volumes and enable auto reload.

## app.py Stage 2 
```py
import time

import redis
from flask import Flask

app = Flask(__name__)
cache = redis.Redis(host='db', port=6379)

def get_hit_count():
    retries = 5
    while True:
        try:
            return cache.incr('hits')
        except redis.exceptions.ConnectionError as exc:
            if retries == 0:
                raise exc
            retries -= 1
            time.sleep(0.5)

@app.route('/')
def hello():
    count = get_hit_count()
    return 'Hello From Project3, version 2 I have been seen {} times.\n'.format(count)

## add a container port 

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=5001)  #by adding a port

```
## docker-compose.yml
``` yml
services:
  frontend:
    build: .
    ports:
      - "9000:5001"
    volumes:
      - .:/app
    environment:
      FLASK_DEBUG: "true" 
        # which means every time container detect the file changes and apply it
  db:
    image: redis:alpine
```
### 1. Build and start the containers

```shell
docker-compose up --build -d
```

### 2. Check containers

```shell
docker ps
```

### 3. Results 

```shell
project3-frontend-1   ...   0.0.0.0:9000->5001/tcp
project3-db-1          ...   6379/tcp
```

### 4. Test by Open in browser

```
http://localhost:9000
```

### 5. Clean up

```
docker compose down --rmi all 
# -v in case to remove volumes as well
```
---
##  Learning Objectives

- Understand multi container apps using Docker Compose.

- Practice container networking (using service names).

- Learn image building & dependency management.

- Improve comfort with Docker CLI and debugging containers.

---
## 👨‍💻 Author

Ali Khaled
🚀 DevOps Student | ☁️ Cloud & Automation Enthusiast

💬 Learning one container at a time!
<p align="center"> <a href="https://github.com/AliKhaledElbaqly" target="_blank"><img src="https://img.shields.io/badge/GitHub-@AliKhaled-black?logo=github"></a> <a href="https://www.linkedin.com/in/alialbaqly/" target="_blank"><img src="https://img.shields.io/badge/LinkedIn-Ali%20Khaled-blue?logo=linkedin"></a> <a href="https://alikhaled.info/" target="_blank"><img src="https://img.shields.io/badge/About_Me-Click_Here-blueviolet?logo=aboutdotme"></a> </p>
<p align="center"> <b>🌍 DevOps Learning Journey — Docker Lab 2025</b> </p> 




<h1 align="center">
  Thanks for visiting my little repo , Take care of yourself 
  <img src="https://raw.githubusercontent.com/aemmadi/aemmadi/master/wave.gif" width="30px">
</h1>

<div align="center">
  
[![Typing SVG](https://readme-typing-svg.demolab.com?font=Fira+Code&size=25&pause=1000&color=00BFFF&center=true&vCenter=true&width=600&lines=No+Vercel+Alternative!!!!;FREE+PALESTINE)](https://git.io/typing-svg)

</div>

<div align="center">
  
<p align="center">
  <img src="https://media1.giphy.com/media/v1.Y2lkPTc5MGI3NjExeHE3bXlhMXh0ZW5jejlzdG85ajVtaGxkNXBqenk1NGt1YWNxMGEzayZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/OG2mixKM4BKsFOpMMv/giphy.gif" alt="Ali Khaled DevOps Banner" width="100%">
</p>


<p align="center">
  <img src="https://media.giphy.com/media/3o7aCTfyhYawdOXcFW/giphy.gif" width="300px">
</p>



---

