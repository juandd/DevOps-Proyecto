pipeline {
    agent any

    stages {
        stage('Compile') {
            steps {
                dir('Servicios/Curso-Microservicios'){
                    sh 'docker build -t microservicio .'
                }
            }
        }
        stage('DBDeploy') {
            steps {
                sh 'docker images'
                sh 'docker run -d -p 8081: microservicio'
            }
        }
    }
}