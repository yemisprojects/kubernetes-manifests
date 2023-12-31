def COLOR_MAP = [
    'SUCCESS': 'good', 
    'FAILURE': 'danger',
]

pipeline {
    agent any

    options {
        timeout(time: 40, unit: 'MINUTES')
    }

    environment {
        DOCKER_REGISTRY = "yemisiomonijo/vprofileapp"
    }

    stages {

        stage('FileSystem scan') {
            steps {
                sh "trivy fs --scanners vuln,config . | tee helm_filesystem_scanresults.txt"
                sh "trivy fs --scanners vuln,config --severity LOW --exit-code 0 --clear-cache ." //UPDATE EXIT CODE TO FAIL
            }
        }

        stage('Update K8s manifest') {

            steps {
                    script {
                            withCredentials([usernamePassword(credentialsId: 'github_token', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                            sh "git config user.email jenkins@gmail.com"
                            sh "git config user.name jenkins"
                            sh "cat webapp/templates/vprofile-app-deployment.yml"
                            sh "sed -i 's|image: ${DOCKER_REGISTRY}:.*|image: $DOCKER_REGISTRY:${DOCKER_TAG}|g' webapp/templates/vprofile-app-deployment.yml"
                            sh "cat webapp/templates/vprofile-app-deployment.yml"
                            sh "git add webapp/templates/vprofile-app-deployment.yml"
                            sh "git commit -m 'Update done by Jenkins Job ${env.JOB_NAME} Build No: ${env.BUILD_NUMBER}'"
                            sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/kubernetes-manifests.git HEAD:main"
                        }
                    }
                
            }
        }


    }

    post {

        always {
            echo 'Slack Notifications'
            slackSend channel: '#k8s-jenkins-cicd',
                color: COLOR_MAP[currentBuild.currentResult],
                message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
            
            emailext attachLog: true,
                subject: "'${currentBuild.result}'",
                body: "Project: ${env.JOB_NAME}<br/>" +
                        "Build Number: ${env.BUILD_NUMBER}<br/>" +
                        "URL: ${env.BUILD_URL}<br/>",
                to: 'dummyuser@yahoo.com',
                attachmentsPattern: 'helm_filesystem_scanresults.txt'

            cleanWs(    
                    cleanWhenNotBuilt: false,
                    cleanWhenAborted: true, cleanWhenFailure: true, cleanWhenSuccess: true, cleanWhenUnstable: true,
                    deleteDirs: true,
                    disableDeferredWipeout: true,
                    notFailBuild: true
            )
        }

    }

    
}
