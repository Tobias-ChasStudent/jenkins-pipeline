pipeline {
    agent any
    triggers {
        githubPush() // This will listen for GitHub push events
    }

    tools {
        nodejs 'NodeJS' // Specify the Node.js installation name you configured
    }
    
    stages {
        
        stage('Build') {
            steps {
                echo 'Building..'
                sh 'npm install' //För ESLint
            }
        }
        stage('Test') {
            steps {
                 echo 'Validating HTML...'
                script {
                    def result= sh(script: "curl -H 'Content-Type: text/html; charset=utf-8' --data-binary @index.html https://validator.w3.org/nu/?out=gnu", returnStdout: true).trim()
                    echo "HTML Validation Result: ${result}"
                    if(result.contains("error") || result.contains("warning")) {
                        error("HTML Validation Failed! Check the above output for details.")
                    }
                }
                echo 'Linting JS file...'
                sh 'npm run test'
            }
        }
        stage('Deploy') {
    steps {
        echo 'Deploying....'
        
        // Assuming you have the credentials set up in Jenkins as 'github-credentials'
        withCredentials([usernamePassword(credentialsId: '3c64daa6-e7c9-4527-ac6b-384c63712780', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
            // Checkout production branch
            sh 'git checkout production'
            // Merge changes from the branch you tested on.
            // NOTE: If you don't want to merge, and you're sure the branch you tested is what you want in production, you can skip this step.
            sh 'git merge origin/main'
            // Configure Git credential helper
            sh 'git config --global credential.helper store'
            sh 'git config --global credential.useHttpPath true'
            // Create temporary Git credentials file
            sh "echo 'https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com' > .git/credentials"

            // Check credentials
            echo "GIT_USERNAME: ${GIT_USERNAME}"
            echo "GIT_PASSWORD: $GIT_PASSWORD"
            
            // Push to production branch
            sh 'git push origin production'
            // Remove temporary Git credentials file
            sh 'rm .git/credentials'
        }
    }
}

    }
}
