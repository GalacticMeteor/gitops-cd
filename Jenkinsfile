pipeline {
    agent {label 'jenkins-agent'}
    
    parameters {
        string(name: 'IMAGE_TAG', defaultValue: 'latest', description: 'Docker image tag to deploy')
    }
    
    environment {
        APP_NAME = "devops-app"
        IMAGE_TAG = "${params.IMAGE_TAG}"
    }
    
    stages {
        stage("Cleanup workspace"){
            steps {
                cleanWs()
            }
        }
        
        stage("Checkout from SCM") {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/GalacticMeteor/gitops-cd.git'
            }
        }
        
        stage("Update the deployment tags") {
            steps {
                dir('app') {
                    sh """
                        cat webapp-deployment.yaml
                        sed -i 's|${APP_NAME}:.*|${APP_NAME}:${IMAGE_TAG}|g' webapp-deployment.yaml
                        cat webapp-deployment.yaml
                    """
                }
            }
        }
        
        stage("Push the changed deployment file to Git") {
            steps {
                dir('app') {
                    sh """
                        git config --global user.name "galacticmeteor"
                        git config --global user.email "hafsichiheb@gmail.com"
                        git add webapp-deployment.yaml
                        git commit -m "Updated Deployment Manifest to ${IMAGE_TAG}"
                    """
                    withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                        sh "git push https://github.com/GalacticMeteor/gitops-cd.git main"
                    }
                }
            }
        }
    }
}