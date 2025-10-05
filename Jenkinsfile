pipeline {
    agent any
       
    environment {
        SPRING_IMAGE_NAME = 'rahmabensaid/events-project'
        SPRING_IMAGE_TAG = '2.0.0'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/rahmabensaidd/devOpsworkshop.git'
            }
        }

        stage('Build') {
            steps {
                // Génère un JAR exécutable
                sh "mvn clean package -DskipTests"
            }
        }

        stage('Test') {
            steps {
                sh "mvn test"
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('jenkins-sonar') {
                    sh "mvn sonar:sonar"
                }
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials',
                                                  usernameVariable: 'DOCKER_USER',
                                                  passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker build -t ${SPRING_IMAGE_NAME}:${SPRING_IMAGE_TAG} ."
            }
        }

        stage('Docker Push') {
            steps {
                sh "docker push ${SPRING_IMAGE_NAME}:${SPRING_IMAGE_TAG}"
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                sh '''
                    docker compose down
                    docker compose pull
                    docker compose up -d
                '''
            }
        }
    }
}
