pipeline {
    agent any

    tools {
        maven 'M3'  // Assurez-vous que Maven est configuré dans "Global Tool Configuration"
    }

    environment {
        SONAR_HOST_URL = 'http://localhost:9000'
        // Si vous avez configuré un token SonarQube dans Jenkins :
        // SONAR_TOKEN = credentials('sonar-token')
    }

    stages {
        stage('Checkout Git') {
            steps {
                echo "🔄 Récupération du code depuis GitHub"
                checkout scm  // Utilise automatiquement le webhook
            }
        }

        stage('Maven Clean') {
            steps {
                echo "🧹 Nettoyage du projet"
                sh 'mvn clean'
            }
        }

        stage('Maven Compile') {
            steps {
                echo "🔨 Compilation du code"
                sh 'mvn compile'
            }
        }

        stage('Tests Unitaires') {
            steps {
                echo "🧪 Exécution des tests"
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo "📊 Analyse de la qualité du code avec SonarQube"
                withSonarQubeEnv('sonarqube') {  // Nom de la configuration SonarQube dans Jenkins
                    sh 'mvn sonar:sonar -Dsonar.projectKey=jenkins-project -Dsonar.projectName="Jenkins Project"'
                }
            }
        }

        stage('Build Package') {
            steps {
                echo "📦 Création du package JAR"
                sh 'mvn package -DskipTests'
            }
        }

        stage('Save Git Info') {
            steps {
                echo "💾 Sauvegarde des informations Git"
                script {
                    def commit = sh(script: 'git log -1 --pretty=format:"%H"', returnStdout: true).trim()
                    def author = sh(script: 'git log -1 --pretty=format:"%an"', returnStdout: true).trim()
                    def message = sh(script: 'git log -1 --pretty=format:"%s"', returnStdout: true).trim()
                    def date = sh(script: 'git log -1 --pretty=format:"%ad"', returnStdout: true).trim()

                    sh """
                    echo "Repository: ${env.GIT_URL}" > github-info.txt
                    echo "Branch: ${env.GIT_BRANCH}" >> github-info.txt
                    echo "Commit: ${commit}" >> github-info.txt
                    echo "Author: ${author}" >> github-info.txt
                    echo "Message: ${message}" >> github-info.txt
                    echo "Date: ${date}" >> github-info.txt
                    echo "Build: ${env.BUILD_NUMBER}" >> github-info.txt
                    """
                }
            }
        }
    }

    post {
        always {
            echo "📎 Archivage des artefacts"
            archiveArtifacts artifacts: 'target/*.jar,github-info.txt', fingerprint: true
            archiveArtifacts 'github-info.txt'
            
            // Nettoyage
            sh 'mvn clean'
        }
        success {
            echo "✅ Pipeline exécutée avec succès!"
            // mail to: 'arwabenamar2004@gmail.com',
            //      subject: "SUCCÈS - Build ${env.JOB_NAME} #${env.BUILD_NUMBER}",
            //      body: "La pipeline a réussi. Voir: ${env.BUILD_URL}"
        }
        failure {
            echo "❌ Pipeline a échoué!"
            // mail to: 'arwabenamar2004@gmail.com',
            //      subject: "ÉCHEC - Build ${env.JOB_NAME} #${env.BUILD_NUMBER}",
            //      body: "La pipeline a échoué. Voir: ${env.BUILD_URL}"
        }
        changed {
            echo "🔄 Statut du build modifié"
        }
    }
}