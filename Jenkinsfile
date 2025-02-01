pipeline {
    agent any

    stages {
        stage('Get Code') {
            steps {
                // Obtener el codigo
				git branch: 'feature_fix_coverage', url: 'https://github.com/Gerardo-j-Gonzalez/Ejercicio1'
				bat 'dir'
				echo WORKSPACE
            }
        }
		
	    stage('Unit') {
            steps {
                catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                    //Ejecucion unica de pytest, para no lanzarlo 2 veces
                    bat '''
                        set PYTHONPATH=%WORKSPACE%
                        coverage run --branch --source=app --omit=app\\__init__.py,app\\api.py -m pytest --junitxml=result-unit.xml test\\unit
                        coverage xml
                    '''
                }
            }
        }


    stage('Rest') {
        steps {
            catchError(buildResult: 'SUCCESS', stageResult: 'SUCCESS') {
                bat '''
                    set FLASK_APP=app\\api.py
                    start flask run
                    start java -jar D:\\UNIR\\wiremock-standalone-3.10.0.jar --port 8888
                    
                    ping -n 10 127.0.0.1
                    
                    pytest --junitxml=result-rest.xml test\\rest

                    //Tratamos los resultados de pytest con junit
                    //junit 'result*.xml'
                '''
            }
        }
    }        

    stage ('Results') {
        steps {
            junit 'result*.xml'
        }
    }   
    stage('Static') {
		steps {
			bat '''
				flake8 --exit-zero --format=pylint app >flake8.out
				'''

			recordIssues tools: [flake8(name: 'Flake8', pattern: 'flake8.out')], qualityGates: [[threshold: 8, type: 'TOTAL', unstable: true], [threshold: 10, type: 'TOTAL', unstable: false]]
		    
		}
	}

    stage('Security') {
		steps {
			// Se pone exit-zero para que no de error al encontrar algun problema, y se pare la ejecucion
			bat '''
				bandit --exit-zero -r . -f custom -o bandit.out --msg-template "{abspath}:{line}: [{test_id}] {msg}"
				'''

			recordIssues tools: [pyLint(name: 'Bandit', pattern: 'bandit.out')], qualityGates: [[threshold: 2, type: 'TOTAL', unstable: true], [threshold: 4, type: 'TOTAL', unstable: false]]
		}
		}

		stage('Coverage') {
			steps {
				bat '''
					coverage run --branch --source=app --omit=app\\__init__.py,app\\api.py -m pytest test\\unit
					coverage xml
					'''

				catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE'){
					cobertura coberturaReportFile: 'coverage.xml', conditionalCoverageTargets: '90,0,80', lineCoverageTargets: '95,0,85'
				}
			}
		}



        stage('Performance') {
    		steps {
			    //Se levanta el flask 
			    catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE'){
                    bat '''
                        set FLASK_APP=app\\api.py
                        start flask run
                        ping -n 5 127.0.0.1
                    '''
                }	

			    // Hay que poner exit-zero para que no se pare la ejecucion cuando se encuentre algun error
    			bat '''
    				//Se pone la rutadonde tengas el ejecutable de jmeter
				    D:\\apache-jmeter-5.6.3\\bin\\jmeter -n -t test\\jmeter\\flask.jmx -f -l flask.jtl
    			'''
    			perfReport sourceDataFiles: 'flask.jtl'    			
    		}
		}


    }
}