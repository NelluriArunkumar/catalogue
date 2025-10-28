pipeline {
    agent {
        label 'AGENT-1'
    }

    environment {
        appVersion = ''
    }

    options {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds() //It is used to disable the parallel Builds of the job
    }

    // parameters {
    //     string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')
    //     text(name: 'BIOGRAPHY', defaultValue: '', description: 'Enter some information about the person')
    //     booleanParam(name: 'TOGGLE', defaultValue: true, description: 'Toggle this value')
    //     choice(name: 'CHOICE', choices: ['One', 'Two', 'Three'], description: 'Pick something')
    //     password(name: 'PASSWORD', defaultValue: 'SECRET', description: 'Enter a password') 
    // }


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
        stage('Test'){
            steps{
                script{
                    echo 'Testing..'
                }
            }
        }
        
    }

    post {
        always {
            echo 'I will always say hello again'
            deleteDir()
        }

        success {
            echo 'Hello Success'
        }

        failure {
            echo "Hello Failure"
        }
    }
}
