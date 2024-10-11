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
                    git credentialsId: 'github-pat', url: 'https://github.com/lmazurczak/abcd-student', branch: 'main'
                }
            }
        }

// -----
        
        stage('[ZAP] Baseline passive-scan') {
    steps {
        sh '''
            docker run --name juice-shop -d --rm \
                -p 3000:3000 \
                bkimminich/juice-shop
            sleep 5 || true
            
        '''
        sh '''
           docker run --name zap --rm\
            --add-host=host.docker.internal:host-gateway \
            -v /home/lukasz/abcd-lab/resources/DAST/zap:/zap/wrk/:rw \
            -t ghcr.io/zaproxy/zaproxy:stable bash -c \
            "zap.sh -cmd -addonupdate; zap.sh -cmd -addoninstall communityScripts -addoninstall pscanrulesAlpha -addoninstall pscanrulesBeta -autorun /zap/wrk/passive_scan.yaml" \
            || true
        '''
    }
    post {
        always {
            sh '''
                docker stop juice-shop || true
                mkdir -p ${WORKSPACE}/results || true
                docker cp zap:/zap/wrk/zap_html_report.html ${WORKSPACE}/results/zap_html_report.html
                docker cp zap:/zap/wrk/zap_xml_report.xml ${WORKSPACE}/results/zap_xml_report.xml
                whoami
                hostname
                ls -la
                
            '''
            defectDojoPublisher(artifact: '/home/lukasz/abcd-lab/resources/DAST/zap/reports/zap_xml_report.xml', 
                    productName: 'Juice Shop', 
                    scanType: 'ZAP Scan', 
                    engagementName: 'l.mazurczak@gmail.com')
        }
    }
}

// -----
    }
}
