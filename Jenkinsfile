pipeline {
    agent any
       
    environment {
        DOCKER_CREDENTIALS_ID = credentials('docker-hub-credentials')
        SPRING_IMAGE_NAME = 'rahmabensaid/events-project'
        SPRING_IMAGE_TAG = '2.0.0'
    }

    stages {
        stage('Github checkout') {
            steps {
                git branch: 'main',
                url: 'https://github.com/rahmabensaidd/devOpsworkshop.git'
            }
        }

        stage("Build") {
            steps {
                sh "mvn clean compile"
            }
        }

        stage("Test") {
            steps {
                sh "mvn test"
            }
        }

        stage("SonarQube Analysis") {
            steps {
                withSonarQubeEnv('jenkins-sonar') {
                    sh "mvn sonar:sonar"
                }
            }
        }

        // Stage "Nexus Upload" supprimée

        stage('Docker Image') {
            steps {
                sh 'docker build -t ${SPRING_IMAGE_NAME}:${SPRING_IMAGE_TAG} .'
            }
        }

        stage('Docker Login') {
            steps {
                sh 'echo $DOCKER_CREDENTIALS_ID_PSW | docker login -u $DOCKER_CREDENTIALS_ID_USR --password-stdin'
            }
        }

        stage('Docker Push') {
            steps {
                sh 'docker push ${SPRING_IMAGE_NAME}:${SPRING_IMAGE_TAG}'
            }
        }

        stage("Deploy with Docker Compose") {
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
