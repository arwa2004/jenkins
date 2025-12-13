pipeline {
    agent any

    // Les outils sont définis ici car nous avons confirmé que cette structure fonctionne sur votre agent.
    tools {
        maven 'M2_HOME'  // Nom exact de votre configuration Maven
        jdk 'JAVA_HOME'  // Nom exact de votre configuration JDK
    }

    environment {
        // Ajout de l'URL du serveur SonarQube pour le scan Maven
        SONAR_HOST_URL = 'http://localhost:9000' 
        // Le token est géré via la configuration du serveur (dans withSonarQubeEnv)
    }

    stages {
        stage('Checkout Git') {
            steps {
                echo "🔄 Récupération du code depuis GitHub"
                checkout scm
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
                sh 'mvn compile -Denforcer.skip=true'
            }
        }

        stage('Tests Unitaires') {
            steps {
                echo "🧪 Exécution des tests"
                sh 'mvn test -Denforcer.skip=true -DskipTests'
            }
        }

        // 🚀 Intégration SonarQube
        stage('SonarQube Analysis') {
            steps {
                echo "📊 Analyse de la qualité du code avec SonarQube"
                // L'argument doit être le NOM exact du serveur SonarQube dans Jenkins (Configuration du Système)
                withSonarQubeEnv('sonarqube') { 
                    sh 'mvn sonar:sonar -Dsonar.projectKey=jenkins-arwa -Dsonar.projectName="Projet Arwa"'
                }
            }
        }

        stage('Build Package') {
            steps {
                echo "📦 Création du package JAR"
                sh 'mvn package -Denforcer.skip=true -DskipTests'
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
            
            // Nettoyage (optionnel)
            // sh 'mvn clean'
        }
        success {
            echo "✅ Pipeline exécutée avec succès!"
        }
        failure {
            echo "❌ Pipeline a échoué!"
        }
    }
}