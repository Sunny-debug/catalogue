pipeline {
    agent {
        node {
            label 'Agent-1'
        }
    }
    environment {
        BUILD_ENV = 'production'
        appVersion = ''
        ACCOUNT_ID = '891377283297'
        ProjectName = 'roboshop'
        ComponentName = 'catalogue'
    }
    options {
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
        stage('Unit Tests') {
            steps {
                script{
                    sh """
                        npm test
                    """
                }
            }
        }
        stage('Sonar Scan'){
            environment {
                    def scannerHome = tool 'sonar-8.0'
            }
            steps {
                script{                 
                     withSonarQubeEnv('sonar-server') 
                    {
                        sh  "${scannerHome}/bin/sonar-scanner"
                    }
                }
            }
        }
        stage('Build the Image') {
            steps {
                script{
                    withAWS(region:'us-east-1',credentials:'aws-creds') {
                        sh """
                            aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${ACCOUNT_ID}.dkr.ecr.us-east-1.amazonaws.com
                            docker build -t ${ACCOUNT_ID}.dkr.ecr.us-east-1.amazonaws.com/${ProjectName}/${ComponentName}:${appVersion} .
                            docker images
                            docker push ${ACCOUNT_ID}.dkr.ecr.us-east-1.amazonaws.com/${ProjectName}/${ComponentName}:${appVersion}

                        """
                    }
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