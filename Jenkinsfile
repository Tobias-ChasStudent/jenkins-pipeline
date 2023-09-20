pipeline {
    agent any
    environment {
        // Bind the credentials to environment variables
        GIT_CREDENTIALS = credentials('3c64daa6-e7c9-4527-ac6b-384c63712780')
    }
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
                script {
                    echo "Entering script portion"
                    echo "Git Credentials: $env.GIT_CREDENTIALS.username" // Debugging output
                    echo "Git Password: ${env.GIT_CREDENTIALS.password}"     // Debugging output
                    echo "Current Directory: ${pwd()}"                 // Debugging output

                    sh """
                        git checkout production
        
                        git merge origin/main
                
                        git config --global credential.helper store
                        git config --global credential.useHttpPath true

                        echo "https://${env.GIT_CREDENTIALS.username}:${env.GIT_CREDENTIALS.password}@github.com" > .git/credentials

                        git push origin production

                        rm .git/credentials
                    """
                }
            }
        }
    }
}
