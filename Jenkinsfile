pipeline {
    agent any

    tools {
        maven 'M2_HOME'  // Assure-toi que Maven est installé sur Jenkins avec ce nom
    }

    stages {

        stage('GIT') {
            steps {
                git branch: 'brahim',
                    url: 'https://github.com/BrahimGarram/devops.git',
                    credentialsId: 'github-token'
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

        stage('MVN PACKAGE') {
            steps {
                sh 'mvn package -DskipTests'  // Génère le JAR dans target/
            }
        }

        stage('MVN SONARQUBE') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                docker build -t tp-foyer-app:1.0 .
                '''
            }
        }
        
        stage('Docker Compose Up') {
            steps {
                // Stop et remove les anciens containers pour éviter conflit
                sh 'docker-compose down'
                sh 'docker-compose up -d --build'
            }
        }

        stage('Docker Run') {
            steps {
                sh '''
                # Stop et remove l'ancien container si existant
                docker stop tp-foyer-container || true
                docker rm tp-foyer-container || true

                # Lancer le nouveau container
                docker run -d \
                  -p 8081:8080 \
                  --name tp-foyer-container \
                  tp-foyer-app:1.0
                '''
            }
        }

    }

    post {
        success {
            echo 'Pipeline terminé avec succès !'
        }
        failure {
            echo 'Pipeline échoué.'
        }
    }
}
