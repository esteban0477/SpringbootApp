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
        stage("build & Push image") {
            docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
            def customImage = docker.build("esteban0477/springbootapp")
            customImage.push()
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