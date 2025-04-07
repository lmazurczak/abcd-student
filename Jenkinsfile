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
	  		ls -la
        	'''
		sh 'semgrep --config=auto --output=${WORKSPACE}/results/semgrep.json --json .'
		    
		sh 'osv-scanner scan --lockfile package-lock.json || true'
		    
                sh 'osv-scanner scan --lockfile package-lock.json --format json --output ${WORKSPACE}/results/sca-osv-scanner.json || true'

		echo 'ls ...'

		sh '''
  			
  			cd ${WORKSPACE}/results 
     			ls -la
			cat semgrep.json
   			cat sca-osv-scanner.json
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
