node {
    checkout scm
	stash includes: 'sources/**/*', name:'sources'
	stage('Build') {
		docker.image('python:2-alpine').inside {
			sh 'python -m py_compile ./sources/add2vals.py ./sources/calc.py'
		}
	}
	stage('Test') {
		docker.image('qnib/pytest').inside {
			sh 'py.test --verbose --junit-xml test-reports/results.xml ./sources/test_calc.py'
		}
		junit 'test-reports/results.xml'
	}
	stage('Manual Approval') {
		input message: 'Lanjutkan ke tahap Deploy?'
    }

}

node('python') {
	unstash 'sources'
	stage('Deploy') {
		docker.image('python:3.9').inside("-u root --entrypoint=''") {
            sh 'pip install pyinstaller'
			sh 'pyinstaller --onefile sources/add2vals.py'
			archiveArtifacts 'dist/add2vals'
			sleep time: 1, unit: 'MINUTES'
		}		
	}		
}