CICD-Pipeline
-----------------------
Workflow Breakdown
This workflow has two jobs:

build → Builds a Docker image and pushes it to DockerHub.

test → Runs tests using pytest in a Python environment.

Understanding Each Section
1️⃣ Workflow Trigger (on: push)
yaml
Copy
Edit
on:
  push:
    branches: [main]
This means the workflow will trigger whenever you push code to the main branch.

Job 1: Build and Push Docker Image
yaml
Copy
Edit
jobs:
  build:
    runs-on: ubuntu-latest
The build job runs on an Ubuntu virtual machine.

🛠 Step 1: Checkout Code
yaml
Copy
Edit
- name: Checkout code
  uses: actions/checkout@v2
Downloads your repository's code so the workflow can use it.

🔑 Step 2: Login to DockerHub
yaml
Copy
Edit
- name: Login to DockerHub
  uses: docker/login-action@v1
  with:
    username: ${{ secrets.DOCKERHUB_USERNAME }}
    password: ${{ secrets.DOCKERHUB_TOKEN }}
Logs into DockerHub using secrets stored in GitHub.

You need to store your DockerHub username and access token in GitHub Secrets.

🐳 Step 3: Build & Push Docker Image
yaml
Copy
Edit
- name: Build, tag, and push image to DockerHub
  env:
    DOCKERHUB_USERNAME: ${{ secrets.DOCKERHUB_USERNAME }}
    IMAGE_NAME: my-image
  run: |
    docker build -t $DOCKERHUB_USERNAME/$IMAGE_NAME .
    docker push $DOCKERHUB_USERNAME/$IMAGE_NAME:latest
Builds a Docker image from your repository's Dockerfile.

Tags the image as DOCKERHUB_USERNAME/my-image.

Pushes it to DockerHub.

✅ After this step, your Docker image is stored on DockerHub and can be used in other projects.

Job 2: Run Tests with Pytest
yaml
Copy
Edit
  test:
    runs-on: ubuntu-latest
The test job runs on a fresh Ubuntu environment.

🛠 Step 1: Checkout Code
yaml
Copy
Edit
- name: Checkout code
  uses: actions/checkout@v2
Downloads your code (same as in the build job).

🐍 Step 2: Set Up Python
yaml
Copy
Edit
- name: Set up Python
  uses: actions/setup-python@v1
  with:
    python-version: '3.x'
Installs Python 3.x on the runner.

📦 Step 3: Install Dependencies
yaml
Copy
Edit
- name: Install dependencies
  run: |
    python -m pip install --upgrade pip
    pip install -r requirements.txt
Upgrades pip (the package manager).

Installs dependencies listed in requirements.txt.

🧪 Step 4: Run Tests Using Pytest
yaml
Copy
Edit
- name: Test with pytest
  run: |
    pip install pytest
    pytest test.py
Installs pytest, a Python testing framework.

Runs pytest test.py to execute tests.

✅ If tests pass, the workflow completes successfully. If tests fail, the workflow stops.

🔍 Summary
Job	Purpose	Key Steps
Build	Build & push Docker image	Checkout → Login to DockerHub → Build → Push
Test	Run Python tests	Checkout → Set up Python → Install dependencies → Run pytest
