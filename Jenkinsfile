pipeline {
    agent any

    tools {
        nodejs 'NodeJS17.4.0'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Test') {
            steps {
                sh 'npm install react-scripts --save'
                // sh 'npm test
            }
        }
        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }
        stage('Build Container') {
            steps {
                script {
                    docker.build("${JOB_NAME}", '-f Dockerfile .')
                }
            }
        }
        stage('Write properties') {
            steps {
                sh "> spinnaker.properties"
                sh "echo 'JOB_NAME=${JOB_NAME}' >> spinnaker.properties"
                sh "echo 'BUILD_ID=${BUILD_ID}' >> spinnaker.properties"
                archiveArtifacts artifacts: 'spinnaker.properties', fingerprint: true
            }
        }
        stage('Push to ECR') {
            steps {
                script {
                    docker.withRegistry('https://219099013464.dkr.ecr.us-west-2.amazonaws.com', 'ecr:us-west-2:spinnaker-admin-aws') {
                        docker.image("${JOB_NAME}").push("${BUILD_ID}")
                    }
                }
            }
        }
    }
}

