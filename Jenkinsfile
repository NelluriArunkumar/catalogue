@Library('jenkins-shared-library') _

def configMap = [
    project : "roboshop",
    component : "catalogue"
]

if(! env.BRANCH_NAME = main ){ //If not equal to main
    nodejsEKSPipeline(configMap) // by default it will call, call function inside this pipeline
}else {
    echo "Please proceed with PROD Process"

}