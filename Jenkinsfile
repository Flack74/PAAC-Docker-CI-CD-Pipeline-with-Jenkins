pipeline {
    agent any

    tools {
        maven "MAVEN3.9"
        jdk "JDK17"
    }

environment {
    registryCredential = 'ecr:<region>:<credential-id>'
    appRegistry = "<aws_account_id>.dkr.ecr.<region>.amazonaws.com/<image_name>"
    vprofileRegistry = "https://<aws_account_id>.dkr.ecr.<region>.amazonaws.com"
    cluster = "<ecs-cluster-name>"
    service = "<ecs-service-name>"
}


    options {
        timestamps()
        skipDefaultCheckout()
    }

    stages {

        stage('📥 Fetch Code') {
            steps {
                echo 'Cloning the repository from GitHub...'
                git branch: 'docker', url: 'https://github.com/hkhcoder/vprofile-project.git'
            }
        }

        stage('🏗️ Build') {
            steps {
                echo 'Building the application with Maven (tests skipped)...'
                sh 'mvn install -DskipTests'
            }
            post {
                success {
                    echo '✅ Build successful. Archiving artifacts...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
                failure {
                    echo '❌ Build failed.'
                }
            }
        }

        stage('🧪 Unit Tests') {
            steps {
                echo 'Running unit tests...'
                sh 'mvn test'
            }
            post {
                failure {
                    echo '❌ Unit tests failed. Please check test reports.'
                }
            }
        }

        stage('🔍 Checkstyle Analysis') {
            steps {
                echo 'Running Checkstyle for code formatting checks...'
                sh 'mvn checkstyle:checkstyle'
            }
        }

        stage('📊 SonarQube Analysis') {
            environment {
                scannerHome = tool 'sonar6.2'
            }
            steps {
                echo 'Running SonarQube analysis...'
                withSonarQubeEnv('sonarserver') {
                    sh '''${scannerHome}/bin/sonar-scanner \
                        -Dsonar.projectKey=vprofile \
                        -Dsonar.projectName=vprofile \
                        -Dsonar.projectVersion=1.0 \
                        -Dsonar.sources=src/ \
                        -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                        -Dsonar.junit.reportsPath=target/surefire-reports/ \
                        -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                        -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
                }
            }
        }

        stage('✅ Quality Gate') {
            steps {
                echo 'Waiting for SonarQube quality gate result...'
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('🐳 Build Docker Image') {
            steps {
                echo "Building Docker image from Dockerfile in ./Docker-files/app/multistage/..."
                script {
                    dockerImage = docker.build("${appRegistry}:${BUILD_NUMBER}", "./Docker-files/app/multistage/")
                }
            }
        }

        stage('📤 Push Docker Image') {
            steps {
                echo "Pushing Docker image to AWS ECR..."
                script {
                    docker.withRegistry(vprofileRegistry, registryCredential) {
                        dockerImage.push("${BUILD_NUMBER}")
                        dockerImage.push("latest")
                    }
                }
            }
        }

        stage('🚀 Deploy to ECS') {
            steps {
                echo "Triggering ECS service deployment..."
                withAWS(credentials: 'awscreds', region: 'us-east-1') {
                    sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
                }
            }
        }
    }

    post {
        always {
            echo '🧹 Pipeline finished. (You can add cleanup logic here)'
        }
    }
}
