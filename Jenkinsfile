pipeline {
    agent any
    options {
        skipDefaultCheckout(true)
    }
    stages {
        stage('Code checkout from GitHub') {
            steps {
                script {
                    cleanWs()
                    git credentialsId: 'gh-token', url: 'https://github.com/kkochel/abcd-student.git', branch: 'main'
                }
            }
        }
        stage('Example - Hello Pawle!') {
            steps {
                echo 'Hello Pawle!'
                echo 'Current workspace'
                sh 'pwd'
                echo 'Listing files in the workspace'
                sh 'ls -la'
            }
        }
        stage('[ZAP] Baseline passive-scan') {
                steps {
                    echo 'Run juice-shop container'
                    sh '''
                        docker run --name juice-shop -d \
                            -p 3000:3000 \
                            bkimminich/juice-shop
                        sleep 5
                    '''
                    echo 'Run zap container'
                    sh '''
		                docker run --name zap \
                            --add-host=host.docker.internal:host-gateway \
                            --mount source=placki,target=/zap/wrk \
                            -t ghcr.io/zaproxy/zaproxy:stable bash -c \
                            "zap.sh -cmd -addonupdate; zap.sh -cmd -addoninstall communityScripts -addoninstall pscanrulesAlpha -addoninstall pscanrulesBeta -autorun /zap/wrk/passive.yaml" \
                            || true
                    '''
                }
                post {
                    always {
                        echo 'Copy reports to workspace'
                        sh '''
                            mkdir -p ${WORKSPACE}/results
                            docker cp zap:/zap/wrk/zap_html_report.html ${WORKSPACE}/results/zap_html_report.html
                            docker cp zap:/zap/wrk/zap_xml_report.xml ${WORKSPACE}/results/zap_xml_report.xml
                        '''
                        echo 'Remove containers'
                        sh '''
                            docker stop zap juice-shop
                            docker rm zap juice-shop
                        '''
                    }
                }
            }
    }
}