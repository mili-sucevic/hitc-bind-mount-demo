# headintheclouds Bind Mounts in Docker

## Introduction
Bind Mounts are a powerful technique in Docker that enables developers to associate directories on the host machine with directories within a container. This method proves particularly useful in development environments where constant modifications are made to files on the host system, and these changes must be instantly reflected within the container. 

## Purpose
This guide aims to demonstrate the usage of Bind Mounts through a real-world example. We'll consider a scenario where you're developing a web application using Docker containers and Flask framework. The objective is to showcase how Bind Mounts facilitate live code changes without the need to rebuild the Docker image repeatedly.

## Example Setup
Let's outline the directory structure of our example Flask web application:

```bash
/hitc-bind-mount-demo
|-- app.py
|-- Dockerfile
|-- requirements.txt
|-- /templates
| |-- index.html
|-- /venv
```

In this setup:
- `app.py` contains the Flask application logic.
- `templates` directory holds HTML templates, with `index.html` being one such template.
- `Dockerfile` is used to build the Docker image for our Flask app.
- `requirements.txt` specifies Python dependencies.

## Getting Started
### Create Flask App
First, let's create a simple Flask application in the `app.py` file:

```python
from flask import Flask, render_template

app = Flask(__name__)

@app.route('/')
def index():
    return render_template('index.html', message='headintheclouds Updated message!')

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0', port=8080)
```

### Create index.html

We'll create a basic HTML template index.html:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flask Web App</title>
</head>
<body>
    <h1>{{ message }}</h1>
    <p>This is a simple Flask web application.</p>
</body>
</html>
```

### Create Dockerfile

Next, let's create a Dockerfile to build the Docker image for our Flask app:

```Dockerfile
FROM python:3.9

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

COPY templates /app/templates

EXPOSE 8080

CMD ["python", "app.py"]
```

### Build Docker Image

Build the Docker image using the following command:

```bash
docker build -t bind-mount-demo .
```

### Run Docker Container with Bind Mount

Run the Docker container with a bind mount to share local code:
```bash
docker run -d --name flask-container -p 8080:8080 -v "$(pwd)":/app bind-mount-demo
```
This command binds the current working directory ($(pwd)) to the /app directory inside the container.

### Access Web App

Open your browser and navigate to http://localhost:8080. You should see your Flask application running.

### Make Code Changes

Open the app.py file on your local machine and make modifications as needed.

### Refresh Browser

After making the code changes, refresh the browser at http://localhost:8080. You should see the updated message without restarting the Docker container.

## Conclusion
In this example, the use of a Bind Mount (-v "$(pwd)":/app) enables seamless synchronisation of the local codebase with the /app directory inside the Docker container. This approach proves invaluable during the development phase, allowing developers to iterate quickly on their code without the need to rebuild the Docker image for every code modification.
