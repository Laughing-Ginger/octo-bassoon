CI/CD Pipeline using Maven + Java + Docker Swarm (Manual / On-Demand)
1. Objective

To design and implement a manual (on-demand) CI/CD pipeline for a Java application using:

Git/GitHub – Version control

Maven – Build & dependency management

Jenkins – CI/CD automation

Docker – Containerization

Docker Swarm – Container orchestration & deployment

2. System Architecture (Explain in Viva)
Developer
   |
   v
GitHub Repository
   |
   v
Jenkins (Manual Trigger)
   |
   |--> Maven Build & Test
   |--> Docker Image Build
   |--> Push Image (Optional)
   v
Docker Swarm
   |
   v
Running Java Application as a Service


📌 Manual / On-Demand means:

Pipeline is triggered manually using “Build Now” in Jenkins

No automatic webhook

3. Prerequisites

Installed and configured on Jenkins server:

Java (JDK 8/11)

Maven

Git

Docker

Jenkins

Docker Swarm initialized

Check versions:

java -version
mvn -version
git --version
docker --version
docker node ls

4. Java + Maven Application Setup
4.1 Sample Maven Project Structure
java-app/
 ├── src/
 │   └── main/java/App.java
 ├── pom.xml
 └── Dockerfile

4.2 Sample pom.xml
<project>
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.devops</groupId>
  <artifactId>java-maven-app</artifactId>
  <version>1.0</version>

  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.8.1</version>
        <configuration>
          <source>8</source>
          <target>8</target>
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>

5. Dockerfile (Containerization)
FROM openjdk:8-jdk-alpine
WORKDIR /app
COPY target/java-maven-app-1.0.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]


📌 Explain:

FROM → Base Java image

COPY → Copy Maven-built JAR

ENTRYPOINT → Run Java app

6. GitHub Repository

Initialize Git repo

git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin <repo-url>
git push -u origin main


📌 Jenkins will pull code from this repo.

7. Jenkins Configuration
7.1 Jenkins Job

Create Pipeline Job

Select Pipeline script from SCM

SCM: Git

Branch: main

Script Path: Jenkinsfile

8. Jenkinsfile (CI/CD Pipeline)
pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/user/java-maven-app.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t java-app:1.0 .'
            }
        }

        stage('Docker Swarm Deploy') {
            steps {
                sh '''
                docker service create \
                --name java_service \
                --replicas 2 \
                -p 8080:8080 \
                java-app:1.0
                '''
            }
        }
    }
}

9. Explanation of Each Pipeline Stage (IMPORTANT FOR VIVA)
🔹 Stage 1: Checkout Code

Jenkins pulls latest code from GitHub

Ensures pipeline works on updated source

📌 Command used:

git clone

🔹 Stage 2: Maven Build

Downloads dependencies

Compiles Java code

Creates .jar file inside target/

📌 Command:

mvn clean package

🔹 Stage 3: Docker Image Build

Dockerfile is executed

JAR file is packaged into Docker image

📌 Command:

docker build -t java-app:1.0 .

🔹 Stage 4: Docker Swarm Deployment

Application deployed as a service

Swarm handles:

Load balancing

Replication

Fault tolerance

📌 Command:

docker service create --name java_service --replicas 2 -p 8080:8080 java-app:1.0

10. Docker Swarm Commands (EXAM GOLD)
docker swarm init
docker service ls
docker service ps java_service
docker node ls


To scale:

docker service scale java_service=5


To remove:

docker service rm java_service

11. Manual / On-Demand Pipeline Explanation

Pipeline is triggered manually from Jenkins dashboard

Useful for:

Controlled releases

Testing environments

Practical exams

📌 Trigger: Build Now


--------

**CI/CD Pipeline using Maven + Java + Docker Swarm

(Cron-Based Automated Pipeline)**

1. Objective

To develop a Java application using Maven and implement a fully automated CI/CD pipeline using:

Git/GitHub – Source code management

Jenkins – Automated CI/CD execution

Maven – Build automation

Docker – Containerization

Docker Swarm – Container orchestration

The pipeline is automatically triggered using Jenkins cron scheduling without manual intervention.

2. Difference: Manual vs Cron-Based Pipeline (Say This in Viva)
Manual Pipeline	Cron-Based Automated Pipeline
Triggered by “Build Now”	Triggered automatically
Human intervention required	Fully automated
Used for testing	Used for production-like setup
No scheduler	Uses Jenkins cron syntax
3. High-Level Architecture
Scheduled Trigger (Cron)
        |
        v
     Jenkins
        |
        |--> Git Checkout
        |--> Maven Build
        |--> Docker Image Build
        |--> Docker Swarm Deployment
        |
        v
 Deployed Java App (Swarm Service)

4. Prerequisites

Installed on Jenkins server:

JDK

Maven

Git

Docker

Jenkins

Docker Swarm initialized

Verify:

docker swarm init
docker node ls

5. Java Maven Application
5.1 Project Structure
java-maven-app/
 ├── src/main/java/App.java
 ├── pom.xml
 ├── Dockerfile
 └── Jenkinsfile

5.2 Sample pom.xml
<project>
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.devops</groupId>
  <artifactId>cron-java-app</artifactId>
  <version>1.0</version>
  <packaging>jar</packaging>

  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.8.1</version>
        <configuration>
          <source>8</source>
          <target>8</target>
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>

6. Dockerfile
FROM openjdk:8-jdk-alpine
WORKDIR /app
COPY target/cron-java-app-1.0.jar app.jar
CMD ["java", "-jar", "app.jar"]


📌 Explain:

Lightweight base image

JAR copied after Maven build

CMD runs the app

7. GitHub Repository

Push all files to GitHub:

git add .
git commit -m "Automated CI/CD with cron trigger"
git push origin main

8. Jenkins Pipeline Configuration
8.1 Jenkins Job

Create Pipeline Job

Choose Pipeline script from SCM

SCM: Git

Branch: main

Script Path: Jenkinsfile

9. Jenkinsfile (Cron-Based Automated Pipeline)
pipeline {
    agent any

    triggers {
        cron('H/5 * * * *')
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/user/cron-java-app.git'
            }
        }

        stage('Build using Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t cron-java-app:1.0 .'
            }
        }

        stage('Deploy to Docker Swarm') {
            steps {
                sh '''
                docker service update --force \
                --image cron-java-app:1.0 \
                cron_service || \
                docker service create \
                --name cron_service \
                --replicas 2 \
                -p 8080:8080 \
                cron-java-app:1.0
                '''
            }
        }
    }
}

10. Cron Trigger Explanation (VERY IMPORTANT)
cron('H/5 * * * *')


📌 Meaning:

H → Jenkins hash (prevents overload)

/5 → Every 5 minutes

Pipeline runs automatically every 5 minutes

Example alternatives:

Every day at midnight:

0 0 * * *


Every hour:

H * * * *

11. Explanation of Each Automated Stage
🔹 Stage 1: Automated Code Checkout

Jenkins automatically pulls latest code on schedule

Ensures pipeline always uses updated source

🔹 Stage 2: Automated Maven Build

Dependencies downloaded

Code compiled

JAR file generated

Command:

mvn clean package

🔹 Stage 3: Automated Docker Build

Docker image created from Dockerfile

New image replaces old version

Command:

docker build

🔹 Stage 4: Automated Docker Swarm Deployment

If service exists → updated

If not → created

Zero downtime deployment using --force

Command:

docker service update --force

12. Docker Swarm Verification
docker service ls
docker service ps cron_service
docker node ls


Access app:

http://<swarm-node-ip>:8080

-----


**CI/CD Pipeline using Maven + Java + Kubernetes

(Manual / On-Demand Pipeline)**

1. Objective

To design and implement a manual (on-demand) CI/CD pipeline for a Maven-based Java application using:

Git/GitHub – Version control

Jenkins – Pipeline execution

Maven – Build automation

Docker – Containerization

Kubernetes – Container orchestration & deployment

The pipeline is manually triggered from Jenkins.

2. Architecture Diagram (Describe / Draw in Exam)
Developer
   |
   v
GitHub Repository
   |
   v
Jenkins (Manual Trigger)
   |
   |--> Code Checkout
   |--> Maven Build
   |--> Docker Image Build
   |--> Push Image (optional)
   v
Kubernetes Cluster
   |
   v
Pod → Deployment → Service → Application

3. Prerequisites

Installed on Jenkins server:

JDK 8/11

Maven

Git

Docker

Jenkins

kubectl

Kubernetes cluster (Minikube / Kubeadm)

Verify:

kubectl version
kubectl get nodes
docker --version

4. Maven Java Application
4.1 Project Structure
java-k8s-app/
 ├── src/main/java/App.java
 ├── pom.xml
 ├── Dockerfile
 ├── deployment.yaml
 └── Jenkinsfile

4.2 pom.xml
<project>
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.devops</groupId>
  <artifactId>java-k8s-app</artifactId>
  <version>1.0</version>

  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.8.1</version>
        <configuration>
          <source>8</source>
          <target>8</target>
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>

5. Dockerfile
FROM openjdk:8-jdk-alpine
WORKDIR /app
COPY target/java-k8s-app-1.0.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]


📌 Explain in viva:
Docker packages the Java app along with runtime into a portable image.

6. Kubernetes Deployment Manifest
6.1 deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: java-k8s-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: java-k8s
  template:
    metadata:
      labels:
        app: java-k8s
    spec:
      containers:
      - name: java-container
        image: java-k8s-app:1.0
        ports:
        - containerPort: 8080

6.2 Service File (Optional but Recommended)
apiVersion: v1
kind: Service
metadata:
  name: java-k8s-service
spec:
  type: NodePort
  selector:
    app: java-k8s
  ports:
    - port: 8080
      targetPort: 8080
      nodePort: 30007

7. GitHub Setup

Push code:

git add .
git commit -m "Java Maven Kubernetes pipeline"
git push origin main

8. Jenkins Job Configuration

Create Pipeline Job

Select Pipeline script from SCM

SCM: Git

Branch: main

Script Path: Jenkinsfile

Trigger: Manual (Build Now)

9. Jenkinsfile (Manual Pipeline)
pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/user/java-k8s-app.git'
            }
        }

        stage('Maven Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t java-k8s-app:1.0 .'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl apply -f deployment.yaml
                kubectl apply -f service.yaml
                '''
            }
        }
    }
}

-----

**CI/CD Pipeline using Maven + Java + Kubernetes

(Cron-Based Automated Pipeline)**

1. Objective

To build a Java application using Maven and implement a fully automated CI/CD pipeline using:

Git/GitHub – Source code management

Jenkins – CI/CD automation

Maven – Build automation

Docker – Containerization

Kubernetes – Container orchestration

The pipeline is automatically triggered using Jenkins cron scheduling and deploys the application to a Kubernetes cluster.

2. Manual vs Cron-Based Pipeline (Say This in Viva)
Manual Pipeline	Cron-Based Pipeline
Triggered manually	Triggered automatically
Requires human action	No human intervention
Used for demos	Used in production
No scheduler	Uses cron expression
3. Architecture Diagram (Explain / Draw)
Cron Trigger (Jenkins)
        |
        v
     Jenkins
        |
        |--> Git Checkout
        |--> Maven Build
        |--> Docker Image Build
        |--> Docker Image Update
        v
 Kubernetes Cluster
        |
        v
 Deployment → Pods → Service → Application

4. Prerequisites

Installed on Jenkins server:

JDK 8/11

Maven

Git

Docker

Jenkins

kubectl

Kubernetes cluster (Minikube / kubeadm)

Verify:

kubectl get nodes
kubectl version
docker --version

5. Java Maven Application Structure
java-k8s-cron-app/
 ├── src/main/java/App.java
 ├── pom.xml
 ├── Dockerfile
 ├── deployment.yaml
 ├── service.yaml
 └── Jenkinsfile

6. Maven Configuration (pom.xml)
<project>
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.devops</groupId>
  <artifactId>java-k8s-cron</artifactId>
  <version>1.0</version>

  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.8.1</version>
        <configuration>
          <source>8</source>
          <target>8</target>
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>

7. Dockerfile
FROM openjdk:8-jdk-alpine
WORKDIR /app
COPY target/java-k8s-cron-1.0.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]


📌 Explain:
Docker bundles the application with all dependencies for consistent execution.

8. Kubernetes Deployment & Service
8.1 Deployment (deployment.yaml)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: java-k8s-cron-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: java-k8s-cron
  template:
    metadata:
      labels:
        app: java-k8s-cron
    spec:
      containers:
      - name: java-container
        image: java-k8s-cron:1.0
        ports:
        - containerPort: 8080

8.2 Service (service.yaml)
apiVersion: v1
kind: Service
metadata:
  name: java-k8s-cron-service
spec:
  type: NodePort
  selector:
    app: java-k8s-cron
  ports:
    - port: 8080
      targetPort: 8080
      nodePort: 30008

9. GitHub Repository

Push everything:

git add .
git commit -m "Automated Kubernetes CI/CD pipeline"
git push origin main

10. Jenkins Pipeline Configuration

Create Pipeline Job

Choose Pipeline script from SCM

SCM: Git

Branch: main

Script path: Jenkinsfile

Triggered automatically via cron

11. Jenkinsfile (Cron-Based Automated Pipeline)
pipeline {
    agent any

    triggers {
        cron('H/10 * * * *')
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/user/java-k8s-cron.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t java-k8s-cron:1.0 .'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl apply -f deployment.yaml
                kubectl apply -f service.yaml
                '''
            }
        }
    }
}

---------

React App Structure
react-app/
 ├── public/
 ├── src/
 ├── package.json
 ├── Dockerfile
 ├── Jenkinsfile
 ├── deployment.yaml   (for Kubernetes)
 ├── service.yaml


Create app:

npx create-react-app react-app
cd react-app


Build:

npm install
npm run build

2️⃣ Dockerfile (React – VERY IMPORTANT)
FROM nginx:alpine
COPY build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]


📌 Explain in viva:
React produces static files → Nginx serves them efficiently.

🧪 PROGRAM 1
React + Docker Swarm (Manual / On-Demand Pipeline)
🔹 Objective

Implement a manual CI/CD pipeline for a React app using GitHub, Jenkins, Docker, and deploy it on Docker Swarm.

🔹 Jenkinsfile (Manual)
pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/user/react-app.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build React App') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t react-app:1.0 .'
            }
        }

        stage('Deploy to Docker Swarm') {
            steps {
                sh '''
                docker service create \
                --name react_service \
                --replicas 2 \
                -p 80:80 \
                react-app:1.0
                '''
            }
        }
    }
}

🔹 Pipeline Stages Explanation

Git checkout

npm install

npm run build

Docker image creation

Swarm service deployment

🧪 PROGRAM 2
React + Docker Swarm (Cron-Based Automated Pipeline)
🔹 Jenkinsfile (Cron Triggered)
pipeline {
    agent any

    triggers {
        cron('H/5 * * * *')
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                url: 'https://github.com/user/react-app.git'
            }
        }

        stage('Install & Build') {
            steps {
                sh '''
                npm install
                npm run build
                '''
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t react-app:1.0 .'
            }
        }

        stage('Deploy to Swarm') {
            steps {
                sh '''
                docker service update --force \
                --image react-app:1.0 react_service || \
                docker service create \
                --name react_service \
                --replicas 2 \
                -p 80:80 \
                react-app:1.0
                '''
            }
        }
    }
}

🔹 Cron Explanation
H/5 * * * * → Runs every 5 minutes automatically

🧪 PROGRAM 3
React + Kubernetes (Manual / On-Demand Pipeline)
🔹 Kubernetes Deployment (deployment.yaml)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: react-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: react-app
  template:
    metadata:
      labels:
        app: react-app
    spec:
      containers:
      - name: react-container
        image: react-app:1.0
        ports:
        - containerPort: 80

🔹 Service (service.yaml)
apiVersion: v1
kind: Service
metadata:
  name: react-service
spec:
  type: NodePort
  selector:
    app: react-app
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30009

🔹 Jenkinsfile (Manual)
pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                url: 'https://github.com/user/react-app.git'
            }
        }

        stage('Install & Build') {
            steps {
                sh '''
                npm install
                npm run build
                '''
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t react-app:1.0 .'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl apply -f deployment.yaml
                kubectl apply -f service.yaml
                '''
            }
        }
    }
}

🧪 PROGRAM 4
React + Kubernetes (Cron-Based Automated Pipeline)
🔹 Jenkinsfile (Automated)
pipeline {
    agent any

    triggers {
        cron('H/10 * * * *')
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                url: 'https://github.com/user/react-app.git'
            }
        }

        stage('Build React App') {
            steps {
                sh '''
                npm install
                npm run build
                '''
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t react-app:1.0 .'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl apply -f deployment.yaml
                kubectl apply -f service.yaml
                '''
            }
        }
    }
}

------

1️⃣ Flask App Structure
flask-app/
 ├── app.py
 ├── requirements.txt
 ├── Dockerfile
 ├── Jenkinsfile
 ├── deployment.yaml   (for Kubernetes)
 └── service.yaml

2️⃣ app.py
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
    return "Hello from Flask CI/CD!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)

3️⃣ requirements.txt
Flask==2.3.2

4️⃣ Dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 5000
CMD ["python", "app.py"]


📌 Explain: Python container + Flask app + port 5000.

🧪 PROGRAM 1: Flask + Docker Swarm (Manual / On-Demand)
Jenkinsfile (Manual Trigger)
pipeline {
    agent any

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/user/flask-app.git'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t flask-app:1.0 .'
            }
        }

        stage('Deploy to Docker Swarm') {
            steps {
                sh '''
                docker service create \
                --name flask_service \
                --replicas 2 \
                -p 5000:5000 \
                flask-app:1.0
                '''
            }
        }
    }
}

🧪 PROGRAM 2: Flask + Docker Swarm (Cron-Based)
pipeline {
    agent any

    triggers {
        cron('H/5 * * * *')
    }

    stages {
        stage('Checkout') {
            steps { git branch: 'main', url: 'https://github.com/user/flask-app.git' }
        }

        stage('Docker Build') {
            steps { sh 'docker build -t flask-app:1.0 .' }
        }

        stage('Deploy to Swarm') {
            steps {
                sh '''
                docker service update --force --image flask-app:1.0 flask_service || \
                docker service create --name flask_service --replicas 2 -p 5000:5000 flask-app:1.0
                '''
            }
        }
    }
}

🧪 PROGRAM 3: Flask + Kubernetes (Manual)
deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: flask-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: flask-app
  template:
    metadata:
      labels:
        app: flask-app
    spec:
      containers:
      - name: flask-container
        image: flask-app:1.0
        ports:
        - containerPort: 5000

service.yaml
apiVersion: v1
kind: Service
metadata:
  name: flask-service
spec:
  type: NodePort
  selector:
    app: flask-app
  ports:
    - port: 5000
      targetPort: 5000
      nodePort: 30010

Jenkinsfile (Manual)
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps { git branch: 'main', url: 'https://github.com/user/flask-app.git' }
        }

        stage('Docker Build') {
            steps { sh 'docker build -t flask-app:1.0 .' }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl apply -f deployment.yaml
                kubectl apply -f service.yaml
                '''
            }
        }
    }
}

🧪 PROGRAM 4: Flask + Kubernetes (Cron-Based)
pipeline {
    agent any

    triggers { cron('H/10 * * * *') }

    stages {
        stage('Checkout') {
            steps { git branch: 'main', url: 'https://github.com/user/flask-app.git' }
        }

        stage('Docker Build') {
            steps { sh 'docker build -t flask-app:1.0 .' }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl apply -f deployment.yaml
                kubectl apply -f service.yaml
                '''
            }
        }
    }
}

----

Flask + Docker Swarm (Manual / Cron)
🔹 Jenkinsfile (WITH Docker Hub)
pipeline {
    agent any

    triggers {
        cron('H/5 * * * *')   // remove for manual pipeline
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/user/flask-app.git'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t mydockerhubuser/flask-app:1.0 .'
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                sh 'docker push mydockerhubuser/flask-app:1.0'
            }
        }

        stage('Deploy to Docker Swarm') {
            steps {
                sh '''
                docker service update --force \
                --image mydockerhubuser/flask-app:1.0 flask_service || \
                docker service create \
                --name flask_service \
                --replicas 2 \
                -p 5000:5000 \
                mydockerhubuser/flask-app:1.0
                '''
            }
        }
    }
}


📌 Docker Swarm automatically pulls the image from Docker Hub if not available locally.

🧪 PROGRAM 3 & 4
Flask + Kubernetes (Manual / Cron)
🔹 Update deployment.yaml (IMPORTANT)
containers:
- name: flask-container
  image: mydockerhubuser/flask-app:1.0
  imagePullPolicy: Always
  ports:
  - containerPort: 5000


📌 imagePullPolicy: Always
→ Forces Kubernetes to pull latest image from Docker Hub

🔹 Jenkinsfile (WITH Docker Hub)
pipeline {
    agent any

    triggers {
        cron('H/10 * * * *')   // remove for manual
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/user/flask-app.git'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t mydockerhubuser/flask-app:1.0 .'
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                sh 'docker push mydockerhubuser/flask-app:1.0'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl apply -f deployment.yaml
                kubectl apply -f service.yaml
                '''
            }
        }
    }
}

