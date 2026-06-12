pipeline {
    agent {
        node {
            label 'Agent-1'
        }
    }
    environment {
        BUILD_ENV = 'production'
        appVersion = ''
    }
    options {
        timeout(time: 60, unit: 'SECONDS')
        disableConcurrentBuilds()
    }
    // This is a sample Jenkins pipeline that demonstrates the use of parameters, environment variables, and stages. It includes a build stage, a test stage, and a deploy stage. The pipeline also has post actions to handle different outcomes of the pipeline execution.
    stages {
        stage('Read version') {
            steps {
                script{
                        def packageJson = readJSON file: 'package.json'
                        appVersion = packageJson.version
                        echo "Read version from package.json: ${appVersion}"
                }
            }
        }
        stage('Install dependencies') {
            steps {
                script{
                    sh """
                        npm install
                    """
                }
            }
        }
        stage('Build the Image') {
            steps {
                script{
                    sh """
                        docker build -t catalogue:${appVersion} .
                        docker images
                    """
                }
            }
        }
        stage('Deploy') {
            steps {
                script{
                    sh """
                    echo "Running deploy script..."
                    """
                }
            }
        }
    }
    post {
        always {
            echo "Pipeline completed."
            cleanWs()
        }
        success {
            echo "Pipeline succeeded!"
        }
        failure {
            echo "Pipeline failed."
        }
        aborted {
            echo "Pipeline aborted."
        }
    }
}