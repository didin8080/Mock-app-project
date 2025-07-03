# Deploy Netflix Clone on Cloud using Jenkins - DevSecOps Project!

### **Phase 1: Initial Setup and Deployment**

**Step 1: Launch EC2 (Ubuntu 22.04):**

- Provision an EC2 instance on AWS with Ubuntu 22.04, t2.large, 30 GB.
- Open below ports 
  - Type: All TCP, Protocol: TCP, Port range: 0 - 65535
  - Type: All ICMP - IPv4, Protocol: ICMP, Port range: All
  - Type: SSH, Protocol: TCP, Port range: 22
  - Type: Custom TCP, Protocol: TCP, Port range: 3000
  - Type: Custom TCP, Protocol: TCP, Port range: 8081
  - Type: Custom TCP, Protocol: TCP, Port range: 8080
  - Type: HTTPS, Protocol: TCP, Port range: 443
  - Type: Custom TCP, Protocol: TCP, Port range: 6443
  - Type: HTTP, Protocol: TCP, Port range: 80

- Connect to the instance using SSH.

**Step 2: Clone the repository**

- Update all the packages

```bash
sudo su
sudo apt-get update
```
- Clone the repository

```bash
 git clone https://github.com/didin8080/Mock-app-project.git
```

**Step 3: Install Jenkins**

```bash
sudo apt update -y
wget -O - https://packages.adoptium.net/artifactory/api/gpg/key/public | tee /etc/apt/keyrings/adoptium.asc
echo "deb [signed-by=/etc/apt/keyrings/adoptium.asc] https://packages.adoptium.net/artifactory/deb $(awk -F= '/^VERSION_CODENAME/{print$2}' /etc/os-release) main" | tee /etc/apt/sources.list.d/adoptium.list
sudo apt update -y
sudo apt install temurin-17-jdk -y
/usr/bin/java --version
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update -y
sudo apt-get install jenkins -y
sudo systemctl start jenkins
sudo systemctl status jenkins
```

**Step 4:Install Docker**

```bash
sudo apt-get update
sudo apt-get install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \ $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins
sudo -u jenkins docker ps
```

**Step 5: Install Python and Docker compose**

```bash
sudo apt-get update
sudo apt-get install -y python3-venv python3-pip
```

```bash
sudo apt-get update
sudo apt-get install -y docker-compose-plugin
```

**Step 6: Install Trivy**

```bash
sudo apt-get install wget apt-transport-https gnupg
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb generic main" | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy
```
**Step 7: Install Docker scout**

Before installing make sure to Login to DockerHub account in browser.

```bash
docker login -u <DockerHubUserName> # It ask for a password provide dockerhub password
```

Installation of Docker Scout 

```bash
curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s -- -b /usr/local/bin
sudo chmod 777 /var/run/docker.sock
```

**Step 8: Install Sonarqube using docker**

```bash
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
docker images
docker ps
```

Access SonarQube, after opening port 9000 

Default username and Password: admin

Set new password


**step 9: Install Necessary Plugins in Jenkins**

Goto Manage Jenkins --> Plugins --> Available Plugins -->

Install below plugins:
  - SonarQube scanner
  - Docker
  - Docker Commons
  - Docker Pipeline
  - Docker API
  - docker-build-step
  - Pipeline stage view
  - Email Extension Template
  - Kubernetes, Kubernetes CLI
  - Kubernetes Client API
  - Kubernetes Credentials
  - Kubernetes Credentials Provider
  - Config File Provider
  - Prometheus metrics

Restart when no jobs are running.


**Step 10: Tools Coniguration in Jenkins**

Goto Manage Jenkins --> tools -->

 - SonarQube Scanner Installations --> Add 'SonarQube Scanner' --> Name: sonar-scanner, 'Check' Install automatically, Version: Select the latest version 7.1.0.4889 
 - Docker Installations --> Add Docker--> Name: docker, 'Check' Install automatically,  Click on 'Add Installer', Select 'Download from docker.com', Version: latest
 - Apply --> Save

**Step 11: configure Sonarqube token**

Goto Sonarqube --> administration --> user --> token 3dots --> give name (token) --> generate --> copy the token 


**step 11: Add Credentials in jenkins**

Goto Jenkins Dashboard --> Manage Jenkins --> Credentials --> global --> Add Secret Text --> paste the token --> give name (sonar-token) --> add credentials


Add Credentials --> Add username and password --> provide username and password of docker hub --> give name (docker) --> add it


**step 12: System Configuration in Jenkins for SonarQube**

Goto SonarQube console --- Click on 'Administration' --- Click on 'Security' --- Select 'Users' --- You can see 'Tokens' --- Click on 3 dashes icon --- A New dialogue --- Name: token, Expires in: 90days --- Generate --- You can see token in green colour. Copy it. eg:(Token: squ_655a181a2da0ce3bfd088ff588e1e282f7c71a1c)

Creation of SonarQube webhooks ----> Administration ----> 'Configuration' dropdown ----> Webhooks ----> Click on 'Create' ----> Name: jenkins, URL: <JenkinsURL>:8080/sonarqube-webhook/ ---> Create

Manage Jenkins ---> System Configuration ---> System ---> Scroll down to 'SonarQube servers' ---> Click on 'Add SonarQube'  ---> Name: sonar, Server URL: paste the SonarQube url. Paste only upto 9000. Dont keep / at the end, Server Authentication Token: Select the 'sonar-token' from drop down ---> Apply ---> Save


**step 13:  Accessing the MySQL Database**

**Connect to MySQL container**

```bash
docker exec -it mysql_db mysql -u root -p
```
Enter password: rootpass when prompted

**Once connected to MySQL:**

**-- Show all databases**

```sql
SHOW DATABASES;
```

**-- Use your application database**

```sql
USE devops_exam;
```

**-- Show all tables**

```sql
SHOW TABLES;
```

**-- View the structure of your results table**

```sql
DESCRIBE results;
```

**-- View all exam results**

```sql
SELECT * FROM results;
```

**- View results sorted by score (highest first)**

```sql
SELECT * FROM results ORDER BY score DESC;
```

**-- View average score**

```sql
SELECT AVG(score) FROM results;
```

**- View number of submissions**

```sql
SELECT COUNT(*) FROM results;
```

## Configure CI/CD Pipeline in Jenkins

```bash
pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "didin8080/devopsexamapp:latest"
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git url: 'https://github.com/didin8080/devops-exam-app.git',
                    branch: 'master'
            }
        }

        stage('File System Scan') {
            steps {
                sh "trivy fs --security-checks vuln,config --format table -o trivy-fs-report.html ."
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh """
                    ${SCANNER_HOME}/bin/sonar-scanner \
                    -Dsonar.projectName=devops-exam-app \
                    -Dsonar.projectKey=devops-exam-app \
                    -Dsonar.sources=. \
                    -Dsonar.language=py \
                    -Dsonar.python.version=3 \
                    -Dsonar.host.url=http://localhost:9000
                    """
                }
            }
        }

        stage('Verify Docker Compose') {
            steps {
                sh '''
                docker compose version || { echo "Docker Compose not available"; exit 1; }
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('backend') {
                    script {
                        withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                            sh "docker build -t ${DOCKER_IMAGE} ."
                            // Push the image to Docker Hub if needed
                            sh "docker push ${DOCKER_IMAGE}"
                        }
                    }
                }
            }
        }

        stage('Docker Scout Image Analysis') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                        sh "docker-scout quickview ${DOCKER_IMAGE}"
                        sh "docker-scout cves ${DOCKER_IMAGE}"
                        sh "docker-scout recommendations ${DOCKER_IMAGE}"
                    }
                }
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                sh '''
                # Clean up any existing containers
                docker compose down --remove-orphans || true
                
                # Start services with build
                docker compose up -d --build
                
                # Wait for MySQL to be ready
                echo "Waiting for MySQL to be ready..."
                timeout 120s bash -c '
                while ! docker compose exec -T mysql mysqladmin ping -uroot -prootpass --silent;
                do 
                    sleep 5;
                    docker compose logs mysql --tail=5 || true;
                done'
                
                # Additional wait for full initialization
                sleep 10
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                echo "=== Container Status ==="
                docker compose ps -a
                echo "=== Testing Flask Endpoint ==="
                curl -I http://localhost:5000 || true
                '''
            }
        }
    }

    post {
        success {
            echo '🚀 Deployment successful!'
            sh 'docker compose ps'
            archiveArtifacts artifacts: 'trivy-fs-report.html', allowEmptyArchive: true
        }
        failure {
            echo '❗ Pipeline failed. Check logs above.'
            sh '''
            echo "=== Error Investigation ==="
            docker compose logs --tail=50 || true
            '''
        }
        always {
            sh '''
            echo "=== Final Logs ==="
            docker compose logs --tail=20 || true
            '''
            archiveArtifacts artifacts: 'trivy-fs-report.html', allowEmptyArchive: true
        }
    }
}
```

Application is deployed in docker. You can access it using publicip with portnumber 5000.

`<publicip:5000>`