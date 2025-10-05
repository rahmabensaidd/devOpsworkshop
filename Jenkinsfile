pipeline {
    agent any
       
    environment {
      
        DOCKER_CREDENTIALS_ID = credentials('docker-hub-credentials')
        SPRING_IMAGE_NAME = 'bensalahons/events-project'
        SPRING_IMAGE_TAG = '2.0.0'
    }

    stages {
        stage('Github checkout') {
            steps {
                git branch: 'master',
                url: 'https://github.com/OnsBENSALAH/devOpsWinterWorkshop.git'
                
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
                withSonarQubeEnv('sq_env') {
                    sh "mvn sonar:sonar"
                }
            }
        }
    
        stage("Nexus Upload") {
            steps {
                sh 'mvn deploy -Dmaven.test.skip=true'
            }
        }
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
