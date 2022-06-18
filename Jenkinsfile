pipeline {
    agent any
    tools{
        maven 'maven_3_8_6'
    }

    stages {
        //Validacion utilizando servidor SonarQube
        
        //El proyecto compila con un dockerfile multistage
        stage('Compile') {
            steps {
                dir('Servicios/Curso-Microservicios'){
                    sh 'docker build -t microservicio .'
                }
            }
        }
        //Se sube la imágen a nexus
        stage('Push Image') {
            steps {
                withCredentials([[$class: 'UsernamePasswordMultiBinding', 
                                credentialsId: 'docker-nexus', 
                                usernameVariable: 'USERNAME', 
                                passwordVariable: 'PASSWORD']]){
                    sh 'docker login -u $USERNAME - p $PASSWORD'
                    sh 'docker push microservicio:lastest'
                }
            }
        }
    }
}