Date: 19/01/2026

Task:8

Objective:

Students will learn how to containerize an application using Docker, manage Docker images and containers, and understand basic DevOps workflows.

✅ Requirements

Part 1: Environment Setup

Install Docker on your system.


Part 2: Create a Simple Application

A static HTML website using Nginx

The application must:

Run locally

Display a message like:
"Hello from Docker – DevOps Task"

Part 3: Dockerfile Creation

Create a Dockerfile.

The Dockerfile must include:

Base image

Working directory

Copy application files

Install dependencies (if any)

Expose required port

CMD or ENTRYPOINT


Part 4: Build & Run Docker Image

Build the Docker image:

docker build -t devops-docker-task .


Run the container:

docker run -d -p 8080:8080 devops-docker-task


Access the application in a browser.

Part 5: Docker Commands Practice

📤 Submission

Source code

Dockerfile

README.md

Screenshots
