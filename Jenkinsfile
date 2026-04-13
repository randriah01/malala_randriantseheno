pipeline {
    agent {
        docker {
            image 'maven:3.9.9-eclipse-temurin-17'
        }
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo "Build : #${env.BUILD_NUMBER}"
                sh 'mvn clean install'
            }
        }

    }

    post {

        success {
            emailext(
                subject: "✅ SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
Projet  : ${env.JOB_NAME}
Build   : #${env.BUILD_NUMBER}
Branche : ${env.GIT_BRANCH}
URL     : ${env.BUILD_URL}

Consulter les logs : ${env.BUILD_URL}console
""",
                to: 'equipe-dev@monentreprise.fr',
                attachLog: true
            )
        }

        failure {
            emailext(
                subject: "❌ FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
Le build a échoué.

Projet  : ${env.JOB_NAME}
Build   : #${env.BUILD_NUMBER}
URL     : ${env.BUILD_URL}

Logs : ${env.BUILD_URL}console
""",
                to: 'equipe-dev@monentreprise.fr',
                attachLog: true
            )
        }

        fixed {
            emailext(
                subject: "🔧 FIXED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Le build est redevenu stable : ${env.BUILD_URL}",
                to: 'equipe-dev@monentreprise.fr'
            )
        }
    }
}