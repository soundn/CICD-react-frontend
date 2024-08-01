pipeline {
    agent {
        label 'Linode'
    }
    
    triggers {
        githubPush()
    }
    
    tools {
        nodejs 'nodejenkins'
    }

    stages {
        stage('test') {
            steps {
                echo 'Hello World'
            }
        }
        
        stage('checkout') {
            steps {
                git branch: 'aitech', url: 'https://github.com/soundn/CICD-react-frontend.git'
            }
        }
        
        stage('check Node') {
            steps {
                sh 'node --version'
                sh 'npm --version'
            }
        }
        
        stage('install dependencies') {
            steps {
                sh 'npm install'
            }
        }
        
        stage('build') {
            steps {
                sh 'npm run build'
            }
        }
        
        stage('Archive Artifact') {
            steps {
                archiveArtifacts artifacts: 'dist/**/*', fingerprint: true
            }
        }
    
        
        stage('deploy') {
            steps {
                sshagent(credentials: ['linode-server-id']) {
                    sh '''
                        mkdir -p ~/.ssh
                        ssh-keyscan -H 212.71.250.109 >> ~/.ssh/known_hosts
                        scp -r dist/* root@212.71.250.109:/tmp/react-app
                        
                        ssh -o StrictHostKeyChecking=no root@212.71.250.109 "
                            sudo apt update && sudo apt upgrade -y
                            echo "Successful Cache Update"
                            sudo apt install nginx -y
                            echo "Successful Nginx Installation"
                            sudo systemctl start nginx
                            sudo cp -r /tmp/react-app/* /var/www/html
                            sudo systemctl restart nginx
                        "    
                    '''
                }
            }
        }
    }

    post {
        success {
            slackSend channel: 'C07E0GYJ0KB', message: 'React-frontend built and deployed Successfully!!'
        }

        always {
            slackSend channel: 'C07E0GYJ0KB', message: 'Pipeline was Triggered!!'
        }
      
        failure {
            slackSend channel: 'C07E0GYJ0KB', message: 'React-frontend Deployment Failed o!!'
        }
        
    }
  
}
