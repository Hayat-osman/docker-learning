# docker learning

Through this module, I progressed from running a simple web application locally to building, managing, and deploying multi-container applications using Docker. This repository documents the practical steps I followed and the concepts I learned along the way.

---

## 1. Local Flask Application

I started by creating a basic Python web application using Flask and running it directly on my local machine. This helped me understand the application behaviour before introducing Docker.

- File: `app.py` (using `from flask import Flask`)

![Flask import in app.py](hello_flask/screenshots/importflask.png)

![Flask app running locally in the browser](hello_flask/screenshots/Python-web-app.png) 
Flask app running locally in the browser


## 2. Containerising the Flask Application

After confirming the Flask application worked locally, I containerised it using Docker.

I created a Dockerfile, which acts as a recipe for building a Docker image. From the same directory as the Dockerfile, I built the image and tagged it:
```bash
docker build -t hello-flask . 
```
 then ran the image as a container, mapping port 5000 on my local machine to port 5000 inside the container and running it in detached mode:
```bash
docker run -d -p 5000:5000 hello-flask
```
![Dockerfile for Hello Flask](hello_flask/screenshots/dockerfile1.png)

## 3. linking containers 

I connected my python web app to a MySQL database running in another Docker container. I updated the Flask app so it can talk to the MySQL container using the container name. The app runs a simple query to get the MySQL version and shows it on the web page with the Hello World message. This shows how two Docker containers can communicate with each other.

![mysql](hello_flask/screenshots/mysqldb.png)

## 4. Docker compose 
I created my first docker-compose.yml file to run my Flask app and MySQL database at the same time. This file defines both services in one place, so I do not need to start each container manually.

The web service is built from the Dockerfile in the current directory and runs on port 5000. It depends on the database service, which means Docker Compose starts the MySQL container first. 

![Docker Compose linking Flask and MySQL](hello_flask/screenshots/dockercompose1.png)

## 5. Pushing and Using Docker Images with AWS ECR

I created a private repository in AWS Elastic Container Registry ECR to store my Docker images. After creating the repository, I authenticated Docker with AWS and pushed my Flask MySQL Docker image to ECR. This allowed me to store the image securely in AWS instead of Docker Hub.

![AWS ECR image](hello_flask/screenshots/ecr2.png)

## 6. Optimising Docker Images with Multi Stage Builds

I optimised my Flask MySQL Docker image by using a multi stage build. The original image was very large because it included build tools and dependencies that were only needed during the build process. This made the image slow to build and unnecessary heavy.

I updated the Dockerfile to use multiple stages. The first stage is used to build the application and install all required dependencies. The final stage only includes the files and dependencies needed to run the application. This removes unnecessary tools from the final image.

Using a multi stage build significantly reduced the image size, making it faster to build, pull, and deploy. This is an important optimisation technique used in real world DevOps environments.

![Multi-stage Docker build](hello_flask/screenshots/multistage.png)
