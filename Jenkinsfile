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

    }
}
