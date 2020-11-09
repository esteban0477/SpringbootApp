#!/usr/bin/env groovy

def sendFailureEmail() {
    def content = "Build for branch ${env.BRANCH_NAME} Failed."
    def info = 'Check the attached log or log into jenkins'
    emailext(
        mimeType: "text/html; charset=UTF-8",
        to: "esteban0477@gmail.com",
        recipientProviders: [[$class: 'CulpritsRecipientProvider'], [$class: "RequesterRecipientProvider"]],
        body: "${content}<br/>${info}<br/>",
        subject: "Jenkins: Your build ${env.BRANCH_NAME} - Build #${env.BUILD_NUMBER} - Failed!",
        attachLog: true,
        attachmentsPattern: 'reports.zip'
    )
}

node() {
    try{
        
        stage("Setup"){
            checkout scm
        }
        stage("Test") {
                sh("mvn clean test -U")
        }
        stage("Build") {
                sh("mvn clean package")
        }
        stage("Test & build image") {
            docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                def customImage = docker.build("esteban0477/springbootapp")
                customImage.push()
            }
        }
        stage("Deploying to Kubernetes") {
            withKubeConfig([credentialsId: 'jenkinsrobot', serverUrl: 'https://30DF4B954DDFF28A4D03EF3E7AE93BCC.yl4.us-east-2.eks.amazonaws.com']) {
                sh 'kubectl get all'
            }
        }
    }

}
    catch(error) {
        throw error
		sendFailureEmail()
    } finally {
        cleanWs()
    }
}