@Library('my-library@v1.6') _
pipeline {
    agent any
    environment{
        APK = "${WORKSPACE}/app/build/outputs/apk/efesFlavor/debug/com.afwsamples.testdpc_9.0.8-9008_minAPI21(nodpi)_apkmirror.com.apk"
        distributionGroup = 'testers'
        NVM_DIR = "${env.HOME}/.nvm"
        NODE_PATH = "$NVM_DIR/versions/node/v22.8.0"
    }
    stages {
       
        stage('Publish') {
            steps {
                script {
                    env.PATH = "${NODE_PATH}/bin:${env.PATH}"
                    def apkUrl = 'https://raw.githubusercontent.com/testingstuff190/apk-test-2/main/com.afwsamples.testdpc_9.0.8-9008_minAPI21(nodpi)_apkmirror.com.apk'
                    def destination = "${WORKSPACE}/app/build/outputs/apk/efesFlavor/debug/com.afwsamples.testdpc_9.0.8-9008_minAPI21(nodpi)_apkmirror.com.apk"

                    // Create directory if it doesn't exist
                    sh """
                    mkdir -p "${WORKSPACE}/app/build/outputs/apk/efesFlavor/debug"
                    """

                    // Download APK file
                    sh """
                    curl -L -o "${destination}" "${apkUrl}"
                    """
                    
                    withCredentials([file(credentialsId: 'firebase-service-account-json', variable: 'GOOGLE_APPLICATION_CREDENTIALS'), string(credentialsId: 'FIREBASE_APP_ID', variable: 'FIREBASE_APP_ID')]){    
                        myFunction.uploadAppToFirebase("myApp", APK, FIREBASE_APP_ID, "testers", false, "" )
                        sh '''
                        #!/bin/bash

                        # Check Firebase CLI version
                        firebase --version

                        # List Firebase projects
                        firebase projects:list --debug
                        '''
                    }
                }
            }
    
        }
    }
}
