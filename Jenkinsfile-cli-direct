pipeline {
    // agent any // if using any all possible agents need rl-secure installed and initialized
    agent {
        // choose an agent where rl-secure is installed
        label 'scan-rl-secure-dev'
    }

    environment {
        // set this in a global env variable so we can use it in multiple stages
        ARTIFACT2SCAN="target/struts2-showcase.war"
        RLSECURE_DIR="/opt/RLSecure"
    }

    stages {

        stage('# Build') {
            steps {
                withMaven( maven: 'mvn3' ) {
                    sh 'mvn clean package'
                }
            }
        }

        stage('# Test rl-secure') {
            environment {
                // mandatory get the secrets from Jenkins that we will later use in the scan container
                PACKAGE_URL="Apache/struts2-showcase@2.5.28_$BUILD_NUMBER"
            }
            steps {
                sh '''
                    ${RLSECURE_DIR}/rl-secure scan ${ARTIFACT2SCAN} \
                        -s ${RLSECURE_DIR} \
                        -p ${PACKAGE_URL} \
                        --keep-reference

                    ${RLSECURE_DIR}/rl-secure report \
                        -s ${RLSECURE_DIR} \
                        -p ${PACKAGE_URL} \
                        --format all \
                        --output-path RLreport/$BUILD_NUMBER

                    ${RLSECURE_DIR}/rl-secure status \
                        -s ${RLSECURE_DIR} \
                        -p ${PACKAGE_URL} \
                        --return-status \
                        --no-color
                '''
            }
        }
    }

    post {
        always {
            sh '''
                # delete first: so you never look at possible data from a earlier run
                rm -rf RLreport/latest &&
                mkdir -p RLreport/latest &&
                cp -r RLreport/$BUILD_NUMBER/* RLreport/latest/
            '''

            archiveArtifacts artifacts: 'RLreport/latest/*.json',
                onlyIfSuccessful: false

            // note that if the job never succeeds due to a error in jenkins the report link never shows
            // after the first fully ok job the link exists and is updated also on failure
            publishHTML (
                target: [
                    allowMissing: true,
                    alwaysLinkToLastBuild: true,
                    keepAll: false,
                    reportDir: 'RLreport/latest',
                    reportFiles: 'rl-html/sdlc.html',
                    reportName: 'ReversingLabsSdlcReport',
                ]
            )
        }
    }
}
