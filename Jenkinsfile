/* groovylint-disable CompileStatic, DuplicateStringLiteral, NestedBlockDepth, UnusedVariable */
/* groovylint-disable-next-line UnusedVariable */
@Library('my-library') _
pipeline {
    agent any
    tools {
        jdk 'jdk17'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        MY_VARIABLE = 'Netflix'
    }
    stages {
        stage('clean workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout from Git') {
            steps {
                repo()
            }
        }
        stage('Sonarqube Analysis ') {
            steps {
                sonar()
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
                    /* groovylint-disable-next-line DuplicateStringLiteral */
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
