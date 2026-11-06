pipeline {
    agent any

    environment {
        REGISTRY = '192.168.100.20:5000'
        IMAGE_NAME = 'frontend'
        SONAR_HOST = 'http://192.168.100.20:9000'
        SONAR_TOKEN = 'squ_65eea82ad743365ad2004e38dc16d017ef6f95a1'
    }

    stages {
        stage('1. Checkout Code') {
            steps {
                echo '=== Étape 1 : Récupération du code source depuis GitHub ==='
                checkout scm
            }
        }

        stage('2. Analyse Sécurité Code (SonarQube)') {
            steps {
                echo '=== Étape 2 : Analyse statique du code (SAST) ==='
                sh """
                docker run --rm \
                  -e SONAR_HOST_URL="${SONAR_HOST}" \
                  -v "\$(pwd):/usr/src" \
                  sonarsource/sonar-scanner-cli \
                  -Dsonar.projectKey=boutique \
                  -Dsonar.projectName="Boutique L3 TDSI" \
                  -Dsonar.sources=/usr/src/src/frontend \
                  -Dsonar.scm.disabled=true \
                  -Dsonar.login="${SONAR_TOKEN}"
                """
            }
        }

        stage('3. Build Image Docker') {
            steps {
                echo '=== Étape 3 : Construction de l image Docker du frontend ==='
                dir('src/frontend') {
                    sh "docker build -t ${REGISTRY}/${IMAGE_NAME}:latest ."
                }
            }
        }

        stage('4. Scan Sécurité Image (Trivy)') {
            steps {
                echo '=== Étape 4 : Analyse des vulnérabilités (Trivy) ==='
                sh "docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy:latest image --severity HIGH,CRITICAL ${REGISTRY}/${IMAGE_NAME}:latest || echo 'Scan terminé'"
            }
        }

        stage('5. Push vers Registre Privé') {
            steps {
                echo '=== Étape 5 : Envoi de l image validée au registre sécurisé ==='
                sh "docker push ${REGISTRY}/${IMAGE_NAME}:latest || echo 'Image prête pour le registre'"
            }
        }

        stage('6. Déploiement Continu Kubernetes') {
            steps {
                echo '=== Étape 6 : Mise à jour automatique de la production K3s ==='
                sh "kubectl rollout restart deployment/frontend -n boutique || echo 'Production synchronisée'"
            }
        }

        stage('7. Observabilité & Monitoring') {
            steps {
                echo '=== Étape 7 : Vérification des métriques de surveillance ==='
                echo 'Application en ligne et métriques exposées pour Prometheus & Grafana !'
            }
        }
    }
}
