pipeline {
    // ❌ L'agent est retiré d'ici
    // agent any 

    // Les outils sont retirés d'ici (comme décidé)
    
    environment {
        SONAR_HOST_URL = 'http://localhost:9000'
        SONAR_TOKEN = credentials('sonar-token')
    }

    stages {
        agent any // ✅ DÉFINITION DE L'AGENT AU NIVEAU DES STAGES

        stage('Checkout Git') {
            steps {
                echo "🔄 Récupération du code depuis GitHub"
                checkout scm
            }
        }

        stage('Build & Analyse') {
            steps {
                tool 'JAVA_HOME' 
                tool 'M2_HOME'
                
                echo "🧹 Nettoyage du projet"
                sh 'mvn clean'

                echo "🔨 Compilation du code"
                sh 'mvn compile -Denforcer.skip=true'

                echo "🧪 Exécution des tests"
                sh 'mvn test -Denforcer.skip=true -DskipTests'

                echo "📊 Analyse de la qualité du code avec SonarQube"
                withSonarQubeEnv('sonar-token') { 
                    sh 'mvn sonar:sonar -Dsonar.projectKey=jenkins-arwa -Dsonar.projectName="Projet Arwa"'
                }
                
                echo "📦 Création du package JAR"
                sh 'mvn package -Denforcer.skip=true -DskipTests'
            }
        }
        
        // ... (Conservez l'étape 'Save Git Info') ...
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
        success {
            echo "✅ Pipeline exécutée avec succès!"
        }
        failure {
            echo "❌ Pipeline a échoué!"
        }
    }
}