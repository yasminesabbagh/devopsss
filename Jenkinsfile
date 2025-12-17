pipeline {
    agent any
    
    tools {
        maven 'M2_HOME'
        jdk 'JDK11'
    }
    
    environment {
        DOCKER_IMAGE = "yasminesabbagh/devopsss-app:${BUILD_NUMBER}"
        DOCKER_IMAGE_LATEST = "yasminesabbagh/devopsss-app:latest"
        DOCKER_API_VERSION = '1.43'
    }
    
    stages {
        stage('Checkout Git') {
            steps {
                echo '📥 Récupération du code depuis Git...'
                git branch: 'yasmine-sabbagh-5SIM1-G1',
                    url: 'https://github.com/yasminesabbagh/devopsss.git'
            }
        }
        
        stage('Clean') {
            steps {
                echo '🧹 Nettoyage du projet...'
                sh 'mvn clean'
            }
        }
        
        stage('Compile') {
            steps {
                echo '🔨 Compilation du projet...'
                sh 'mvn compile'
            }
        }
        
        stage('Test') {
            steps {
                echo '🧪 Exécution des tests unitaires...'
                sh 'mvn test'
            }
        }
        
        stage('Package') {
            steps {
                echo '📦 Création du package JAR...'
                sh 'mvn package -DskipTests'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                echo '🐳 Construction de l\'image Docker...'
                script {
                    sh "docker build -t ${DOCKER_IMAGE} ."
                    sh "docker tag ${DOCKER_IMAGE} ${DOCKER_IMAGE_LATEST}"
                }
            }
        }
        
        stage('Push to Docker Hub') {
            steps {
                echo '📤 Push de l\'image vers Docker Hub...'
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'dockerhub-credentials', 
                        usernameVariable: 'DOCKER_USER', 
                        passwordVariable: 'DOCKER_PASS'
                    )]) {
                        sh "echo \${DOCKER_PASS} | docker login -u \${DOCKER_USER} --password-stdin"
                        sh "docker push ${DOCKER_IMAGE}"
                        sh "docker push ${DOCKER_IMAGE_LATEST}"
                    }
                }
            }
        }
        
        stage('Deploy with Docker Compose') {
            steps {
                echo '🚀 Déploiement avec Docker Compose...'
                sh '''
                    docker-compose down || true
                    docker-compose up -d
                '''
            }
        }
    }
    
    post {
        success {
            echo '✅ Pipeline exécuté avec succès!'
        }
        failure {
            echo '❌ Le pipeline a échoué.'
        }
        always {
            echo '🧹 Nettoyage de l\'espace de travail...'
            cleanWs()
        }
    }
}
