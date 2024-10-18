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
		sh 'trufflehog git file://. --only-verified'
		
		sh 'trufflehog git file://. --only-verified --json >${WORKSPACE}/results/trufflehog.json '
		
		sh '''
  			cd ${WORKSPACE}/results 
     			ls -la
			cat trufflehog.json
		'''
		    
            }
        
    post {
        always {

          	echo 'Archiving results ...'
	        archiveArtifacts artifacts: 'results/**/*', fingerprint: true, allowEmptyArchive: true
	        
	 	echo 'Sending reports '
		defectDojoPublisher(artifact: 'results/trufflehog.json', 
                productName: 'Juice Shop', 
                scanType: 'Trufflehog Scan', 
                engagementName: 'l.mazurczak@gmail.com')
		
        }
    }
}

// -----
    }
}
