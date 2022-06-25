pipeline {
    agent any
    tools{
        maven 'maven_3_8_6'
    }

    stages {
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
                    sh 'docker login http://192.168.1.11:8083 -u $USERNAME -p $PASSWORD'
                    sh 'docker tag microservicio:latest 192.168.1.11:8083/repository/docker-private/microservicio:latest'
                    sh 'docker push 192.168.1.11:8083/repository/docker-private/microservicio:latest'
                }
            }
        }

        //Correr imágen desde repositorio
        stage('Deploy service') {
            steps {
                withCredentials([[$class: 'UsernamePasswordMultiBinding', 
                                credentialsId: 'docker-nexus', 
                                usernameVariable: 'USERNAME', 
                                passwordVariable: 'PASSWORD']]) {
                    sh 'docker login 192.168.1.11:8083 -u $USERNAME -p $PASSWORD'
                    sh 'docker stop microservicio-one || true'
                    sh 'docker run -d --rm --name microservicio-one -e SPRING_PROFILES_ACTIVE=dev -p 8090:8090 192.168.1.11:8083/repository/docker-private/microservicio:latest'
                }
            }
        }

        //Liquibase
        stage('database') {
            steps {
                dir('liquibase/'){
                    sh '/opt/liquibase/liquibase --changeLogFile="changesets/db.changelog-master.xml" update'
                }
            }
        }

        //Stress
        stage('Stress') {
            steps {
                sleep 5
                dir('GatlingTest/'){
                    sh 'mvn gatling:test -Dgatling.simulationClass=microservice.PingUsersSimulation'
                }
            }
            post {
                always {
                    gatlingArchive()
                }
            }
        }
    }
}