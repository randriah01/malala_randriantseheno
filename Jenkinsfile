pipeline {
    agent any

    parameters {
        string(
            name: 'GIT_COMMIT_SHA',
            defaultValue: 'main',
            description: 'Branche ou SHA du commit à builder'
        )

        choice(
            name: 'ENVIRONMENT',
            choices: ['dev', 'staging', 'prod'],
            description: 'Environnement cible'
        )

        booleanParam(
            name: 'SKIP_TESTS',
            defaultValue: false,
            description: 'Ignorer les tests'
        )
    }

    stages {

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

        stage('Build + Tests parallèles') {
            parallel {

                stage('Tests unitaires') {
                    steps {
                        script {
                            if (!params.SKIP_TESTS) {
                                sh 'mvn test -B'
                            } else {
                                echo "Tests ignorés"
                            }
                        }
                    }
                    post {
                        always {
                            junit '**/surefire-reports/*.xml'
                        }
                    }
                }

                stage('Qualité code') {
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

        stage('Matrix Java') {
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

        stage('Validation avant PROD') {
            when {
                expression { params.ENVIRONMENT == 'prod' }
            }

            steps {
                timeout(time: 1, unit: 'HOURS') {
                    input message: 'Déployer en PRODUCTION ?', ok: 'Oui'
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    if (params.ENVIRONMENT == 'dev') {
                        sh 'echo "Deploy DEV"'
                    } else if (params.ENVIRONMENT == 'staging') {
                        sh 'echo "Deploy STAGING"'
                    } else {
                        sh 'echo "Deploy PROD"'
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Build SUCCESS"
        }

        failure {
            echo "Build FAILED"
        }
    }
}
