pipeline {
    // Jenkins CI/CD pipeline for EcommerceApp - all 4 stages as per docx

    agent any

    tools {
        maven 'maven3.8.2'
    }

    triggers {
        pollSCM('* * * * *')
    }

    options {
        timestamps()
        buildDiscarder(logRotator(numToKeepStr: '5', artifactNumToKeepStr: '5'))
    }

    stages {

        stage('CheckOutCode') {
            steps {
                git branch: 'main', url: 'https://github.com/jeevan0024/EcommerceApp.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                dir('EcommerceApp') {
                    withSonarQubeEnv('SonarQube') {
                        sh 'mvn clean compile sonar:sonar -Dsonar.login=admin -Dsonar.password=Jeevan0024'
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Build') {
            steps {
                dir('EcommerceApp') {
                    sh 'mvn clean package'
                }
            }
        }

        stage('Deploy via Ansible') {
            steps {
                sh 'ansible-playbook -i inventory ansible-deploy.yaml'
            }
        }

    }

    post {
        success {
            emailext to: 'admin@example.com',
                subject: "EcommerceApp Build #${env.BUILD_NUMBER} SUCCESS",
                body: "Build and deployment completed. Build: ${env.BUILD_NUMBER}"
        }
        failure {
            emailext to: 'admin@example.com',
                subject: "EcommerceApp Build #${env.BUILD_NUMBER} FAILED",
                body: "Pipeline failed. Check Jenkins console. Build: ${env.BUILD_NUMBER}"
        }
    }

}
