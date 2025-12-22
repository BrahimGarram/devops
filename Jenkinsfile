pipeline {
    agent any

    tools {
        maven 'M2_HOME'
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
                sh 'mvn package -DskipTests'
            }
        }

        stage('MVN SONARQUBE') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Docker Cleanup & Build') {
            steps {
                sh '''
                docker rm -f tp-foyer-container tp-foyer-mysql prometheus grafana || true
                docker rmi -f tp-foyer-app:1.0 || true
                docker build -t tp-foyer-app:1.0 .
                '''
            }
        }

        stage('Docker Compose Up') {
            steps {
                sh '''
                docker-compose down --remove-orphans
                docker-compose up -d --build
                '''
            }
        }

        /* =======================
           PROMETHEUS SETUP
        ======================= */
        stage('Prometheus Setup') {
            steps {
                script {
                    writeFile file: 'prometheus.yml', text: """
                    global:
                      scrape_interval: 15s

                    scrape_configs:
                      - job_name: 'springboot'
                        metrics_path: '/actuator/prometheus'
                        static_configs:
                          - targets: ['tp-foyer-container:8080']
                    """
                }

                sh '''
                docker run -d --name prometheus \
                    -p 9090:9090 \
                    -v ${PWD}/prometheus.yml:/etc/prometheus/prometheus.yml \
                    --network tp-network \
                    prom/prometheus:latest
                '''
            }
        }

        /* =======================
           GRAFANA SETUP
        ======================= */
        stage('Grafana Setup') {
            steps {
                sh '''
                docker run -d --name grafana \
                    -p 3000:3000 \
                    -e GF_SECURITY_ADMIN_PASSWORD=admin \
                    --network tp-network \
                    grafana/grafana:latest
                '''
            }
        }

        /* =======================
           TEST API REST
        ======================= */
    

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
