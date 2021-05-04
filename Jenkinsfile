pipeline {
    agent any
    stages {

        stage('infracost') {
            agent {
                docker {
                    image 'infracost/infracost:test'
                    // The image needs to run as root as it creates files in ~/.config
                    // Also override the entrypoint to do nothing as we define that in steps below
                    args "--user=root --entrypoint=''"
                }
            }

            environment {
                INFRACOST_API_KEY = credentials('jenkins-infracost-api-key')
                IAC_PATH = 'terraform'
                FAIL_CONDITION = '{"percentage_threshold": 10}'
            }

            steps {
                sh '/scripts/ci/jenkins_diff.sh'

                // This ensures that the 'jenkins' user can cleanup without running into permission issues for
                // files/folders that Terraform created (such as .terraform)
                sh 'chmod -R 777 .'

                publishHTML (target: [
                    allowMissing: false,
                    alwaysLinkToLastBuild: false,
                    keepAll: true,
                    reportDir: './',
                    reportFiles: 'infracost_diff.html',
                    reportName: 'Infracost Diff Output'
                ])

                sh 'rm infracost_diff.html'
            }
        }
    }
}
