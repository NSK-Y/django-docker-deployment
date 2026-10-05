# Dockerized Django Application

A simple Django web application containerized using **Docker**.

This project demonstrates how to build a Docker image, run a Django application inside a Docker container, and expose the application through port `8000`.

## 🛠️ Technologies Used

* Python 3.10
* Django
* Docker
* Linux / Ubuntu
* AWS EC2

## 📁 Project Structure

```text
python-web-app/
├── Dockerfile
├── README.md
├── requirements.txt
└── devops/
    ├── manage.py
    ├── db.sqlite3
    ├── demo/
    │   ├── __init__.py
    │   ├── admin.py
    │   ├── apps.py
    │   ├── models.py
    │   ├── tests.py
    │   ├── urls.py
    │   ├── views.py
    │   ├── migrations/
    │   │   └── __init__.py
    │   └── templates/
    │       └── demo_site.html
    │
    └── devops/
        ├── __init__.py
        ├── asgi.py
        ├── settings.py
        ├── urls.py
        └── wsgi.py
```

> `__pycache__` files are generated automatically by Python and are not required for running the application.

## 🐳 Dockerfile

The Dockerfile is located in the project root and is used to create the Docker image.

Example:

```dockerfile
FROM python:3.10

WORKDIR /app

COPY . .

RUN pip install -r requirements.txt

EXPOSE 8000

CMD ["python", "devops/manage.py", "runserver", "0.0.0.0:8000"]
```

The important point is that `manage.py` is located inside the `devops` directory.

## 🚀 Run the Application Using Docker

### 1. Clone the Repository

Clone the repository:

```bash
git clone <YOUR-GITHUB-REPOSITORY-URL>
```

Move into the project directory:

```bash
cd python-web-app
```

Verify the project structure:

```bash
ls
```

You should see:

```text
Dockerfile
README.md
devops
requirements.txt
```

---

### 2. Build the Docker Image

Run:

```bash
docker build -t python-web-app:v1 .
```

The `.` at the end tells Docker to use the current directory as the build context.

Check whether the image was created successfully:

```bash
docker images
```

You should see something similar to:

```text
REPOSITORY       TAG       IMAGE ID
python-web-app   v1        ...
```

---

### 3. Run the Docker Container

Run the Django application in detached mode:

```bash
docker run -d -p 8000:8000 python-web-app:v1
```

The port mapping means:

```text
EC2 Host Port 8000
        ↓
Docker Container Port 8000
        ↓
Django Application
```

---

### 4. Check the Running Container

Run:

```bash
docker ps
```

You should see a port mapping similar to:

```text
0.0.0.0:8000->8000/tcp
```

This confirms that port `8000` on the EC2 host is mapped to port `8000` inside the Docker container.

---

### 5. Check Container Logs

To verify that Django started successfully:

```bash
docker logs <container-id>
```

For example:

```bash
docker logs <container-id>
```

You should see output similar to:

```text
Starting development server at http://0.0.0.0:8000/
```

---

### 6. Test the Application

The Django application provides the `/demo/` route.

Test it from the EC2 instance:

```bash
curl http://localhost:8000/demo/
```

If the application is running correctly, Django should return the HTML content of the demo page.

You can also test the root URL:

```bash
curl http://localhost:8000/
```

The root URL may return a Django `404` if no route has been configured for `/`.

---

## 🌐 Access From a Browser

If the application is running on an AWS EC2 instance, open:

```text
http://<EC2-PUBLIC-IP>:8000/demo/
```

For example:

```text
http://<EC2-PUBLIC-IP>:8000/demo/
```

Replace `<EC2-PUBLIC-IP>` with the current public IPv4 address of your EC2 instance.

---

## 🔐 AWS Security Group

When running this application on an AWS EC2 instance, port `8000` must be allowed in the instance's Security Group.

Add an inbound rule:

```text
Type:     Custom TCP
Port:     8000
Source:   0.0.0.0/0
```

For learning/testing, `0.0.0.0/0` allows access from the internet.

For production environments, restrict the source IP range or use a reverse proxy such as Nginx instead of exposing the Django development server directly.

---

## 🔍 Useful Docker Commands

### List Docker Images

```bash
docker images
```

### List Running Containers

```bash
docker ps
```

### List All Containers

```bash
docker ps -a
```

### View Container Logs

```bash
docker logs <container-id>
```

Example:

```bash
docker logs nice_thompson
```

### Follow Container Logs

```bash
docker logs -f <container-id>
```

### Stop a Container

```bash
docker stop <container-id>
```

### Remove a Container

```bash
docker rm <container-id>
```

### Remove the Docker Image

```bash
docker rmi python-web-app:v1
```

---

## 🧪 Troubleshooting

### Port 8000 Already Allocated

If you get:

```text
Bind for 0.0.0.0:8000 failed: port is already allocated
```

Check which container is using port `8000`:

```bash
docker ps
```

Stop the existing container:

```bash
docker stop <container-id>
```

Then run the application again:

```bash
docker run -d -p 8000:8000 python-web-app:v1
```

---

### Django Shows 404 at `/`

If you open:

```text
http://localhost:8000/
```

and Django returns:

```text
Page not found
```

try:

```text
http://localhost:8000/demo/
```

The `/demo/` route is provided by the `demo` Django application.

---

### Check Whether the Container Is Running

Run:

```bash
docker ps
```

If the container is not listed, check all containers:

```bash
docker ps -a
```

Then check its logs:

```bash
docker logs <container-id>
```

---

### Rebuild the Image After Making Changes

If you modify the application or Dockerfile, rebuild the image:

```bash
docker build -t python-web-app:v1 .
```

Then remove the old container if necessary:

```bash
docker rm -f <container-id>
```

Run the new container:

```bash
docker run -d -p 8000:8000 python-web-app:v1
```

---

## 📌 What This Project Demonstrates

This project demonstrates the basic Docker workflow for a Django application:

```text
Django Application
       ↓
    Dockerfile
       ↓
   docker build
       ↓
   Docker Image
       ↓
    docker run
       ↓
 Docker Container
       ↓
     Port 8000
       ↓
   Web Browser
```

### Docker Workflow

```text
Source Code
    ↓
Dockerfile
    ↓
Docker Build
    ↓
Docker Image
    ↓
Docker Container
    ↓
Django Application
    ↓
HTTP Request
    ↓
/demo/
```

## 👨‍💻 Author

**Naveen Sri Krishna**

DevOps | Cloud | SRE Enthusiast
