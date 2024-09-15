@Library('my-library') _
pipeline {
    agent any
    environment{
        APK = "${WORKSPACE}/app/build/outputs/apk/efesFlavor/debug/com.afwsamples.testdpc_9.0.8-9008_minAPI21(nodpi)_apkmirror.com.apk"
        distributionGroup = 'testers'
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/testingstuff190/testing-firebase.git', credentialsId: 'github-token'
            }
        }
        stage('Publish') {
            steps {
                script {
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
                    
                    withCredentials([file(credentialsId: 'firebase-service-account-json', variable: 'SERVICE_ACCOUNT_JSON'), string(credentialsId: 'FIREBASE_APP_ID', variable: 'FIREBASE_APP_ID')]){    
                        // Install Firebase CLI
                        sh '''
                        #!/bin/bash
                        export GOOGLE_APPLICATION_CREDENTIALS=${SERVICE_ACCOUNT_JSON}

                        # Check Firebase CLI version
                        firebase --version

                        # Make sure the PATH is updated correctly
                        export PATH="$PATH:$HOME/.nvm/versions/node/v18.20.4/bin"

                        # Print Firebase App ID for debugging
                        echo "Using Firebase App ID: ${FIREBASE_APP_ID}"

                        # List Firebase projects
                        firebase projects:list --debug

                        # Distribute APK using Firebase CLI
                        firebase appdistribution:distribute ${APK} --app ${FIREBASE_APP_ID}  --groups ${distributionGroup} --debug
                        '''
                    }
                }
            }
    
        }
    }
}
