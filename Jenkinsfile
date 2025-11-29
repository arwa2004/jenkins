pipeline {
    agent any

    tools {
        maven 'M3'  // Assurez-vous que Maven est configuré dans Jenkins
    }

    environment {
        SONAR_HOST_URL = 'http://localhost:9000'
        // Ajoutez votre token SonarQube si nécessaire
        // SONAR_AUTH_TOKEN = credentials('sonar-token')
    }

    stages {
        stage('Checkout Git') {
            steps {
                echo "Récupération du projet depuis Git"
                git branch: 'main', url: 'https://github.com/arwa2004/jenkins.git'
                // Remplacez par votre URL Git
            }
        }

        stage('Maven Clean') {
            steps {
                echo "Nettoyage avec Maven"
                sh 'mvn clean'
            }
        }

        stage('Maven Compile') {
            steps {
                echo "Compilation avec Maven"
                sh 'mvn compile'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo "Analyse de la qualité du code avec SonarQube"
                sh 'mvn sonar:sonar -Dsonar.projectKey=mon-projet -Dsonar.projectName="Mon Projet"'
                // Adaptez projectKey et projectName selon votre projet
            }
        }

        stage('Tests Unitaires') {
            steps {
                echo "Exécution des tests unitaires"
                sh 'mvn test'
            }
        }

        stage('Build Package') {
            steps {
                echo "Création du package"
                sh 'mvn package -DskipTests'
            }
        }
    }

    post {
        always {
            // Archive les résultats des tests et rapports
            junit 'target/surefire-reports/*.xml'
            archiveArtifacts 'target/*.jar'
            
            // Sauvegarde les infos Git
            script {
                def commit = sh(script: 'git log -1 --pretty=format:"%H"', returnStdout: true).trim()
                def author = sh(script: 'git log -1 --pretty=format:"%an"', returnStdout: true).trim()
                def message = sh(script: 'git log -1 --pretty=format:"%s"', returnStdout: true).trim()

                sh """
                echo "Commit: ${commit}" > git-info.txt
                echo "Author: ${author}" >> git-info.txt
                echo "Message: ${message}" >> git-info.txt
                """
                archiveArtifacts 'git-info.txt'
            }
        }
        success {
            echo "Pipeline exécutée avec succès!"
            // mail to: 'votre-email@example.com',
            //      subject: "SUCCÈS - Pipeline ${env.JOB_NAME}",
            //      body: "La build ${env.BUILD_NUMBER} a réussi."
        }
        failure {
            echo "Pipeline a échoué!"
            // mail to: 'votre-email@example.com',
            //      subject: "ÉCHEC - Pipeline ${env.JOB_NAME}",
            //      body: "La build ${env.BUILD_NUMBER} a échoué."
        }
    }
}