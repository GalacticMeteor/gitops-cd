pipeline {
    agent {label 'jenkins-agent'}
    envirement {
        APP_NAME = "devops-app"
    }

    stages {
        stage("cleanup workspace"){
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM")
        {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/GalacticMeteor/gitops-cd.git'
            }
        }

        stage("Update the deployment tags") {
            steps {
                dir('app') {
                    sh """
                        cat webapp-deployment.yaml
                        sed -i 's/${APP_NAME}.*/${APP_Name}:${IMAGE_TAG}/g' webapp-deployment.yaml
                        cat webapp-deployment.yaml
                    """
                }
            }
        }

        stage("Push the changed deployment file to Git") {
            steps {
                sh """
                   git config --global user.name "galacticmeteor"
                   git config --global user.email "hafsichiheb@gmail.com"
                   git add deployment.yaml
                   git commit -m "Updated Deployment Manifest"
                """
                withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                  sh "git push https://github.com/GalacticMeteor/gitops-cd.git main"
                }
            }
        }

    }
}