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
                script {
                    echo "Entering script portion"

                    sh """
                        git remote -v
                    
                        git checkout production

                        git config --global credential.username Tobias-ChasStudent
                        
                        git merge origin/main

                        git push origin production

                        rm .git/credentials
                    """
                }
            }
        }
    }
}
