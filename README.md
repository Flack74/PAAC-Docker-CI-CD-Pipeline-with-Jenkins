# 🐳 **PAAC Docker**

**PAAC Docker** is a Jenkins pipeline project that automates the full CI/CD lifecycle of a Java-based application using Docker and AWS ECS. This pipeline ensures smooth, efficient, and automated deployment of applications in a containerized environment.

## 🔧 **Technologies Used**

- ⚙️ **Jenkins** (Declarative Pipeline)
- 🛠️ **Maven 3.9**
- ☕ **JDK 17 and 21**
- 📊 **SonarQube** (Code Quality & Quality Gate)
- ✍️ **Checkstyle** (Code Formatting)
- 🐋 **Docker** & **Amazon ECR** (Image Build & Push)
- 🚀 **AWS ECS** (Deployment)

## 📄 **Jenkins Pipeline Overview**

This pipeline performs the following stages:

1. **📥 Fetch Code** – Clones the source code from the GitHub repository.
2. **🏗️ Build** – Compiles the application and archives `.war` artifacts.
3. **🧪 Unit Tests** – Executes unit tests using Maven.
4. **🔍 Checkstyle Analysis** – Verifies Java code formatting using Checkstyle.
5. **📊 SonarQube Analysis** – Runs static code analysis and coverage reporting.
6. **✅ Quality Gate** – Waits for SonarQube quality gate result.
7. **🐳 Docker Image Build** – Builds a multi-stage Docker image.
8. **📤 Push to ECR** – Pushes the Docker image to Amazon ECR (Elastic Container Registry).
9. **🚀 Deploy to ECS** – Triggers a rolling deployment on AWS ECS.

## 🛠️ **Required Jenkins Configuration**

To run this pipeline successfully, the following must be configured in Jenkins:

| Name            | Description                                     |
|-----------------|-------------------------------------------------|
| `MAVEN3.9`      | Maven tool installation                         |
| `JDK17`         | JDK 17 tool installation                        |
| `sonar6.2`      | SonarQube Scanner tool                          |
| `sonarserver`   | SonarQube server configuration                  |
| `awscreds`      | AWS credentials stored as a Jenkins credential  |

### 🔐 **Environment Variables (Redacted for Security)**

Ensure you replace the placeholders with your actual values for private deployments:

```groovy
environment {
    registryCredential = 'ecr:<region>:<credential-id>'
    appRegistry = "<aws_account_id>.dkr.ecr.<region>.amazonaws.com/<image_name>"
    vprofileRegistry = "https://<aws_account_id>.dkr.ecr.<region>.amazonaws.com"
    cluster = "<ecs-cluster-name>"
    service = "<ecs-service-name>"
}
````

---

## 🚀 **Getting Started**

1. Set up the required tools and credentials in Jenkins.
2. Create a pipeline job and use the provided `Jenkinsfile`.
3. Trigger a build to execute the full CI/CD pipeline.

---

## 📁 **Repository Structure**

```bash
.
├── Docker-files/
│   └── app/
│       └── multistage/       # Contains Dockerfile used in build stage
├── Jenkinsfile               # Declarative pipeline definition
└── README.md                 # Project documentation
```

---

## 📌 **Notes**

* 🌐 Ensure your AWS ECS and ECR resources are active and properly linked.
* 🔑 **Important:** Do **not** commit real credentials or AWS account IDs to version control.

---

## 🤝 **Contributing**

We welcome contributions! If you have ideas, bug fixes, or enhancements, feel free to fork the repository, make changes, and submit pull requests. Please ensure your changes adhere to the project’s structure and pass all pipeline checks.

---

## 📄 **License**

This project is licensed under the [MIT License](LICENSE).
