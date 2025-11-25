pipeline {
    agent any 

    stages {
        stage('Checkout') {
            steps {
                // Cette étape est souvent faite automatiquement si vous utilisez 'Pipeline script from SCM'
                // Mais vous pouvez utiliser la commande 'checkout scm' si besoin
                echo 'Code source récupéré.'
            }
        }
        
        stage('Build - Compilation') {
            steps {
                echo 'Démarrage de la compilation maintenantzz...'
                // Exécuter la commande de compilation de votre projet (ex: Maven, Node, etc.)
                // sh 'mvn clean install'
                // sh 'npm install'
            }
        }
        
        stage('Test') {
            steps {
                echo 'Exécution des tests unitaires...'
                // Exécuter les commandes de test
                // sh 'mvn test'
                // sh 'npm test'
            }
        }
        
        stage('Quality Gate (Optionnel)') {
            steps {
                echo 'Vérification de la qualité du code (ex: SonarQube).'
            }
        }
    }
}