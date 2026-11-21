pipeline {
    agent any

    tools {
        jdk   'JAVA_HOME'
        maven 'M2_HOME'
    }

    triggers {
        githubPush()
    }

    environment {
        EMAIL_TO = 'nayssenk@gmail.com'
    }

    stages {
        stage('Récupération du code (GitHub)') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/nayssenksissa123/DevOps-AppGestionDesProjets.git'
            }
        }

        stage('Tests unitaires') {
            steps {
                dir('backend') {
                    sh 'mvn clean test'
                }
            }
            post {
                always {
                    junit allowEmptyResults: true,
                          testResults: 'backend/target/surefire-reports/*.xml'
                }
            }
        }

        stage('Création du livrable (target)') {
            steps {
                dir('backend') {
                    sh 'mvn package -DskipTests'
                }
            }
        }
    }

    post {
        success {
            archiveArtifacts artifacts: 'backend/target/*.jar', fingerprint: true
            echo 'Build réussi : livrable créé dans backend/target/'
        }
        failure {
            emailext(
                to: "${EMAIL_TO}",
                subject: "ECHEC du build : ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """Le build ${env.JOB_NAME} #${env.BUILD_NUMBER} a échoué.

Consulter les logs : ${env.BUILD_URL}console""",
                attachLog: true
            )
        }
    }
}
