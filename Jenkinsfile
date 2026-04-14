pipeline {

    agent {
        docker {
            image 'maven:3.9.9-eclipse-temurin-17'
            args '-u root'
        }
    }

    parameters {
        string(name: 'GIT_COMMIT_SHA', defaultValue: 'main')
        choice(name: 'ENVIRONMENT', choices: ['dev', 'staging', 'prod'])
        booleanParam(name: 'SKIP_TESTS', defaultValue: false)
    }

    stages {

        // ✅ CHECKOUT
        stage('Checkout') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: "${params.GIT_COMMIT_SHA}"]],
                    userRemoteConfigs: [[
                        url: 'https://github.com/randriah01/malala_randriantseheno.git',
                        credentialsId: 'github-credentials'
                    ]]
                ])
                sh 'git log -1 --oneline'
            }
        }

        // ✅ PARALLEL
        stage('Validation parallèle') {
            parallel {

                stage('Tests unitaires') {
                    when { expression { !params.SKIP_TESTS } }
                    steps {
                        sh 'mvn test -B'
                    }
                }

                stage('Qualité') {
                    steps {
                        sh 'mvn checkstyle:checkstyle pmd:pmd -B'
                    }
                }

                stage('Couverture') {
                    steps {
                        sh 'mvn test jacoco:report -B'
                    }
                }
            }
        }

        // ✅ MATRIX
        stage('Tests multi-Java') {
            matrix {
                axes {
                    axis {
                        name 'JAVA_VERSION'
                        values '17', '21'
                    }
                }

                stages {
                    stage('Test') {
                        steps {
                            sh 'mvn clean test -B'
                        }
                    }
                }
            }
        }

        // ✅ VALIDATION PROD
        stage('Validation avant PROD') {
            when {
                expression { params.ENVIRONMENT == 'prod' }
            }
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    input message: 'Déployer en PROD ?'
                }
            }
        }

        // ✅ DEPLOY
        stage('Deploy') {
            steps {
                sh "echo Deploy vers ${params.ENVIRONMENT}"
            }
        }
    }

    post {
        success {
            echo "SUCCESS"
        }
        failure {
            echo "FAILURE"
        }
    }
}
