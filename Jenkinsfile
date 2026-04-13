/**
 * Jenkinsfile – Pipeline CI complète
 * Projet : Boutique en ligne – ICDE848
 *
 * Ce fichier doit être placé à la RACINE du dépôt Git.
 * Jenkins le détecte automatiquement lors de la création du job Pipeline.
 *
 * Stages :
 *   1. Checkout       → récupère le code depuis Git
 *   2. Build          → compile le code source
 *   3. Tests unitaires → lance *Test.java via Surefire
 *   4. Tests intégration → lance *IT.java via Failsafe
 *   5. Couverture     → génère le rapport JaCoCo
 *   6. Qualité        → Checkstyle + PMD + CPD + SpotBugs
 *   7. Archive        → sauvegarde le JAR dans Jenkins
 *
 * Post :
 *   - failure → email à l'équipe
 *   - fixed   → email quand le build repasse au vert
 */

pipeline {
    agent {
        docker {
            image 'maven:3.9.9-eclipse-temurin-17'
        }
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
    }
}

Projet  : ${env.JOB_NAME}
Build   : #${env.BUILD_NUMBER}
Branche : ${env.GIT_BRANCH}
URL     : ${env.BUILD_URL}

Consulter les logs : ${env.BUILD_URL}console
                """,
                to:          'equipe-dev@monentreprise.fr',
                attachLog:   true
            )
        }

        // Seulement quand le build repasse de FAILURE à SUCCESS
        fixed {
            emailext(
                subject: "✅ FIXED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body:    "Le build est de nouveau stable : ${env.BUILD_URL}",
                to:      'equipe-dev@monentreprise.fr'
            )
        }

    } // fin post

} // fin pipeline
