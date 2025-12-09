pipeline {
    agent any

    environment {
        FIREBASE_TOKEN = credentials('FIREBASE_TOKEN')
    }

    options {
        timestamps()
    }

    triggers {
        // This ensures polling is triggered by GitHub webhook
        pollSCM('* * * * *') // checks every minute; webhook forces it immediately
    }

    stages {

        stage('Checkout Code') {
            steps {
                echo " Starting Git checkout…"
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    doGenerateSubmoduleConfigurations: false,
                    extensions: [
                        [$class: 'CleanBeforeCheckout'], // clean workspace
                        [$class: 'PruneStaleBranch'],    // remove stale branches
                        [$class: 'WipeWorkspace']        // wipe workspace completely
                    ],
                    userRemoteConfigs: [[
                        url: 'https://github.com/Noureenmohamedd/jenkins.git'
                        // public repo → no credentials needed
                    ]]
                ])
                echo "Checkout completed successfully!"
            }
        }

        stage('List Workspace') {
            steps {
                echo "Listing workspace files…"
                sh 'pwd'
                sh 'ls -la'
            }
        }

        stage('Deploy to Firebase') {
            steps {
                echo "Deploying to Firebase hosting..."

                // Firebase CLI already installed globally inside container
                sh 'firebase --version'

                // Deploy from ROOT where firebase.json exists
                sh 'firebase deploy --only hosting --token "$FIREBASE_TOKEN" --debug'
            }
        }
    }

    post {
        success {
            echo "Pipeline finished successfully!"
        }
        failure {
            echo "Pipeline failed!"
        }
    }
}
