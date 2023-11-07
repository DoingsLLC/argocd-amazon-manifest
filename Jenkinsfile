def gitAddResult = sh(script: "git add .", returnStatus: true)
if (gitAddResult != 0) {
    error "Failed to add files to Git."
}

def gitCommitResult = sh(script: "git commit -m 'Done by Jenkins Job changemanifest: ${env.BUILD_NUMBER}'", returnStatus: true)
if (gitCommitResult != 0) {
    error "Failed to commit changes to Git."
}

def gitPushResult = sh(script: "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/DoingsLLC/argocd-amazon-manifest.git HEAD:main", returnStatus: true)
if (gitPushResult != 0) {
    error "Failed to push changes to Git."
}

pipeline {
    agent any

    environment {
        DOCKERTAG = "${env.BUILD_NUMBER}"
        IMAGE = 'doings/netflixapp'
    }

    stages {
        stage('Clone repository') {
            steps {
                git branch: 'main', url: 'https://github.com/DoingsLLC/argocd-amazon-manifest.git'
            }
        }

        stage('Update GIT') {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    withCredentials([usernamePassword(credentialsId: 'Doings_Git_token', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        sh "git config --global user.email peterobijiofor@gmail.com"
                        sh "git config --global user.name doingsllc"
                        sh "cat deployment.yml"
                        sh "sed -i 's+${IMAGE}.*+${IMAGE}:${DOCKERTAG}+g' deployment.yml"
                        sh "cat deployment.yml"
                        sh "git add ."
                        sh "git commit -m 'Done by Jenkins Job changemanifest: ${env.BUILD_NUMBER}'"
                        sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/DoingsLLC/argocd-amazon-manifest.git HEAD:main"
                    }
                }
            }
        }
    }
}
