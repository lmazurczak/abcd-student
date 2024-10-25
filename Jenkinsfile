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
		sh 'semgrep --config=auto --output=/results/semgrep_report.json --json .'


		sh '''
  			cd ${WORKSPACE}/results 
     			ls -la
			cat semgrep_report.json
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
