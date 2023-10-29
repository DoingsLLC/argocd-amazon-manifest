node {
    def app

    env.IMAGE = 'doingsllc/amazon-clone'

    stage('Clone repository') {
        git branch: 'main', url: 'https://github.com/DoingsLLC/argocd-amazon-manifest.git'
    }

    stage('Update GIT') {
        script {
            catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                withCredentials([usernamePassword(credentialsId: 'Doings_Git_token', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                    // script {def encodedPassword = URLEncoder.encode("$GIT_PASSWORD",'UTF-8')}
                    // script  {def IMAGE='doingsllc/amazon-clone'}
                    sh "git config user.email peterobijiofor@gmail.com"
                    sh "git config user.name DoingsLLC"
                    // sh "git switch main" // Use 'main' instead of 'master'

                    sh "cat deployment.yml"
                    sh "sed -i 's+${IMAGE}.*+${IMAGE}:${DOCKERTAG}+g' deployment.yml"
                    sh "cat deployment.yml"

                    // Check if there are changes to commit
                    def hasChanges = sh(returnStatus: true, script: 'git diff-index --quiet HEAD --')

                    if (hasChanges != 0) {
                        // Changes detected, proceed with commit
                        sh "git add ."
                        sh "git commit -m 'Done by Jenkins Job changemanifest: ${env.BUILD_NUMBER}'"
                        sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/argocd-amazon-manifest.git HEAD:main"
                    } else {
                        // No changes to commit, log a message or take appropriate action
                        echo "No changes to commit. Skipping git commit and push steps."
                    }
                }
            }
        }
    }
}
