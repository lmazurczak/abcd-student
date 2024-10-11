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
        
        stage('SCA scan') {

            steps {
		sh '''
        		mkdir -p ${WORKSPACE}/results || true
        	'''
		sh 'osv-scanner --lockfile package-lock.json || true'
		    
                sh 'osv-scanner scan --lockfile package-lock.json --format json --output ${WORKSPACE}/results/sca-osv-scanner.json || true'

		sh '''
  			cd ${WORKSPACE}/results 
     			ls -la
		'''
		    
            }
        
    post {
        always {

          	echo 'Archiving results ...'
	        archiveArtifacts artifacts: 'results/**/*', fingerprint: true, allowEmptyArchive: true
	        
	 	echo 'Sending reports '
		
        }
    }
}

// -----
    }
}
