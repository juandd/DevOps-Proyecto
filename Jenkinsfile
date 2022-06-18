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
                    sh 'docker login 192.168.1.9:8083 -u $USERNAME -p $PASSWORD'
                    sh 'docker tag microservicio:latest 192.168.1.9:8083/repository/docker-private/microservicio:latest'
                    sh 'docker push 192.168.1.9:8083/repository/docker-private/microservicio:latest'
                }
            }
        }
    }
}