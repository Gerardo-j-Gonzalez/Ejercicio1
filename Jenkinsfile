pipeline {
    agent any

    stages {
        stage('Get Code') {
            steps {
                // Obtener el codigo
				git 'https://github.com/Gerardo-j-Gonzalez/Ejercicio1'
				bat 'dir'
				echo WORKSPACE
            }
        }

	stage('Unit') {
	steps {
		catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE'){
			bat '''
				set PYTHONPATH=.
				pytest --junitxml=result-unit.xml test\\unit
			'''   
			junit 'result*.xml'
			}
		}
	}


	stage('Rest') {
	    steps {
		catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
		    bat '''
			set FLASK_APP=app\\api.py
			start flask run
			start java -jar D:\\UNIR\\wiremock-standalone-3.10.0.jar --port 9090 --root-dir test\\wiremock
			
			ping -n 10 127.0.0.1
			
			pytest --junitxml=result-rest.xml test\\rest
		    '''
		}
	    }
	}        

    }
}
