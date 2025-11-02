pipeline {
    agent {
        label 'AGENT-1'
    }

    environment {
        appVersion = ''
        REGION = 'us-east-1'
        ACC_ID = '850960379432'
        PROJECT = 'roboshop'
        COMPONENT = 'catalogue'
    }

    options {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds() //It is used to disable the parallel Builds of the job
    }

    parameters {
        booleanParam(name: 'deploy', defaultValue: false, description: 'Toggle this value')
        
    }


    //Build
    stages {
        stage('Read package.json'){
            steps{
                script {
                    def packageJson = readJSON file: 'package.json'
                    appVersion = packageJson.version
                    echo "Package Version = ${appVersion}"
                }
               
            }
        }
        stage('Install Dependencies'){
            steps{
                script{
                    sh """
                        npm install
                    """
                }
            }
        }
        stage('Unit Testing'){
            steps{
                script{
                    sh """
                        echo "Unit testing"
                    """
                }
            }
        }

        stage('Sonar Scan'){
            environment{
                scannerHome = tool 'sonar-7.2'
            }
            steps{
                script{
                    //Sonar server environment
                    withSonarQubeEnv(installationName: 'sonar-7.2'){
                        sh "${scannerHome}/bin/sonar-scanner"
                    }
                }
            }
        }

        //Enable webhook in sonarqube server and wait for results
        stage('Quality Gate'){
            steps {
                timeout(time: 1, unit: 'HOURS'){
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        

        stage('Docker Build'){
            steps{
                script{
                    withAWS(credentials: 'aws_creds', region: 'us-east-1') {
                        sh """
                            aws ecr get-login-password --region ${REGION} | docker login --username AWS --password-stdin ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com
                            docker build -t ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion} .
                            docker push ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion}
                        """
                    }
                }
            }
        }
        stage('Trigger Deploy'){
            when{
                expression { params.deploy }
            }
            steps{
                script {
                    build job: 'catalogue-cd',
                    parameters: [
                        string(name: 'appVersion', value: "${appVersion}"),
                        string(name: 'deploy_to', value: 'dev')
                    ],
                    propagate: false,
                    wait: false
                }
            }

        }
        
    }

    post {
        always {
            echo 'I will always say hello again'
            //deleteDir()
        }

        success {
            echo 'Hello Success'
        }

        failure {
            echo "Hello Failure"
        }
    }
}
