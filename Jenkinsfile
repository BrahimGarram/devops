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
                # Supprimer l'ancienne image si elle existe
                docker rmi -f tp-foyer-app:1.0 || true

                # Build de la nouvelle image
                docker build -t tp-foyer-app:1.0 .
                '''
            }
        }

        stage('Docker Compose Up') {
            steps {
                sh '''
                # Stop et remove les anciens containers + supprimer orphelins
                docker-compose down --remove-orphans

                # Build et lancement des containers
                docker-compose up -d --build
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
