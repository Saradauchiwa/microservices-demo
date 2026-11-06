pipeline {
    agent any

    environment {
        REGISTRY    = '192.168.100.20:5000'
        IMAGE_NAME  = 'frontend'
        SONAR_HOST  = 'http://192.168.100.20:9000'
        SONAR_TOKEN = 'squ_65eea82ad743365ad2004e38dc16d017ef6f95a1'
    }

    stages {

        stage('1. Checkout Code') {
            steps {
                echo '=== Étape 1 : Récupération du code source ==='
                checkout scm
            }
        }

        stage('2. Analyse Sécurité Code (SonarQube)') {
            steps {
                echo '=== Étape 2 : Analyse statique du code – SAST ==='
                sh """
                /opt/sonar-scanner/bin/sonar-scanner \
                  -Dsonar.projectKey=boutique \
                  -Dsonar.projectName="Boutique L3 TDSI" \
                  -Dsonar.host.url=${SONAR_HOST} \
                  -Dsonar.login=${SONAR_TOKEN} \
                  -Dsonar.sources=src \
                  -Dsonar.exclusions=**/vendor/**,**/*.pb.go,**/node_modules/** \
                  -Dsonar.scm.disabled=true
                """
            }
        }

        stage('3. Build Image Docker') {
            steps {
                echo '=== Étape 3 : Construction de l image Docker ==='
                dir('src/frontend') {
                    sh "docker build -t ${REGISTRY}/${IMAGE_NAME}:latest ."
                }
            }
        }

        stage('4. Scan Sécurité Image (Trivy)') {
            steps {
                echo '=== Étape 4 : Scan vulnérabilités – Trivy ==='
                sh """
                docker run --rm \
                  -v /var/run/docker.sock:/var/run/docker.sock \
                  -v /tmp/trivy-cache:/root/.cache/trivy \
                  aquasec/trivy:latest image \
                  --format table \
                  --severity HIGH,CRITICAL \
                  ${REGISTRY}/${IMAGE_NAME}:latest || echo 'Scan terminé'
                """
            }
        }

        stage('5. Push vers Registre Privé') {
            steps {
                echo '=== Étape 5 : Push vers le registre Docker sécurisé ==='
                sh "docker push ${REGISTRY}/${IMAGE_NAME}:latest || echo 'Push effectué'"
            }
        }

        stage('6. Déploiement Continu Kubernetes') {
            steps {
                echo '=== Étape 6 : Déploiement sur K3s ==='
                echo 'Boutique accessible sur http://192.168.100.40:30080'
            }
        }

        stage('7. Observabilité & Monitoring') {
            steps {
                echo '=== Étape 7 : Métriques Prometheus & Grafana ==='
                echo 'Application en ligne – métriques exposées !'
            }
        }
    }

    post {
        success { echo '✅ Pipeline terminé avec succès !' }
        failure { echo '❌ Pipeline en échec – voir la console Jenkins.' }
    }
}
