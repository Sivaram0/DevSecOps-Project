
pipeline {
    agent any
    tools {
        jdk 'jdk17'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }
    stages {
        stage('clean workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout from Git') {
            steps {
                git branch: 'main', url: 'https://github.com/N4si/DevSecOps-Project.git'
            }
        }
        stage('Sonarqube Analysis ') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Netflix \
                    -Dsonar.projectKey=Netflix '''
                }
            }
        }
        stage('TRIVY FS SCAN') {
            steps {
                sh 'trivy fs . > trivyfs.txt'
            }
        }
        stage('Docker Build & Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                        sh 'docker build --build-arg TMDB_V3_API_KEY=4b44f3548a8cd7c67f528d7acf819e87 -t netflixer .'
                        sh 'docker tag netflixer sivaramaprasaditrajula/netflixer:latest '
                        sh 'docker push sivaramaprasaditrajula/netflixer:latest '
                    }
                }
            }
        }
        stage('TRIVY') {
            steps {
                sh 'trivy image sivaramaprasaditrajula/netflixer:latest > trivyimage.txt'
            }
        }
        stage('Deploy to container') {
            steps {
                sh 'docker run -d --name netflix -p 8081:80 sivaramaprasaditrajula/netflixer:latest'
            }
        }
    }
}
