pipeline {
    agent any

    stages {
        stage('Hello') {
            steps {
                echo 'Hello Barhoum'
            }
        }
        stage('MVN CLEAN') {
            steps {
                sh 'mvn clean'
            }
        }

        stage('MVN COMPILE') {
            steps {
                sh 'mvn compile'
            }
        } 
    }
}
