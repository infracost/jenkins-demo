pipeline {
    agent any
    stages {

        stage('infracost') {
            agent {
                docker {
                    image 'infracost/infracost:test'
                    args '--user=root --entrypoint='
                }
            }

            environment {
                INFRACOST_API_KEY = credentials('jenkins-infracost-api-key')
                IAC_PATH = 'terraform'
                FAIL_CONDITION = '{"percentage_threshold": 10}'
            }

            steps {
                sh '/scripts/ci/jenkins_diff.sh'
                sh 'chmod -R 777 .'
                publishHTML (target: [
                    allowMissing: false,
                    alwaysLinkToLastBuild: false,
                    keepAll: true,
                    reportDir: './',
                    reportFiles: 'infracost_diff_output.html',
                    reportName: 'Infracost Diff Output'
                ])
            }
        }
    }
}
