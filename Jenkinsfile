pipeline {
    // Uncomment this section if you'd like to use a different agent configuration
    // agent {
    //     node {
    //         label 'docker_agent_python'
    //     }
    // }

    agent {
        node {
            label 'my-host-agent' // Use the label assigned in the configuration
        }
    }

    environment {
        GIT_CREDENTIALS_ID = 'github' // ID for GitHub credentials
    }

    stages {
        stage('Clone Repository') {
            steps {
                checkout scm
            }
        }

        stage('Update GIT') {
            steps {
                script {
                    catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                        withCredentials([usernamePassword(credentialsId: "${GIT_CREDENTIALS_ID}", passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                            // Update Git configurations and files
                            sh """
                                git config user.email "farouk.echcharef@usmba.ac.ma"
                                git config user.name "Farouk-Echaref"
                                echo "Before updating deployment.yaml:"
                                cat deployment.yaml
                                sed -i 's+faroukcha69/ci_image.*+faroukcha69/ci_image:${DOCKERTAG}+g' deployment.yaml
                                echo "After updating deployment.yaml:"
                                cat deployment.yaml
                                git add .
                                git commit -m "Done by Jenkins Job changemanifest: ${env.BUILD_NUMBER}"
                                git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/CD_REPO.git HEAD:main
                            """
                        }
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed.'
        }
        success {
            echo 'Pipeline succeeded. Deployment.yaml updated and changes pushed to GitHub.'
        }
        failure {
            echo 'Pipeline failed during execution.'
        }
    }
}
