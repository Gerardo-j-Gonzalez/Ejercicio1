pipeline {
    agent any

    stages {
        stage('GetCode') {
            steps {
                git 'https://github.com/Gerardo-j-Gonzalez/Ejercicio1'
            }
        }
        stage('Comentario') {
            steps {
                echo 'Prueba de comentario con ECHO'
            }
        }
        stage('Verificacion de WorkSpace') {
            steps {
                bat "dir"
                echo "El directorio workspace es ${workspace}"
            }
        }
        stage('Build') {
            steps {
                echo 'Estoy en la etapa Build'
            }
        }
        stage('Paralelo'){
            parallel{
                stage('Unit') {
                    steps {
                        catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE'){
                            bat '''
                                set PYTHONPATH=%WORKSPACE%
                                pytest --junitxml=result-unit.xml test\\unit
                            '''   
                        }
                    }
                }
                stage('Servicios') {
                    steps {
                        catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE'){
                            bat '''
                                set FLASK_APP=app\\api.py
                                start flask run
                                SET PYTHONPATH=%WORKSPACE%
                                pytest --junitxml=result-serv.xml test\\unit
                            '''   
                        }
                    }
                }        
            }
        }
        

        stage('Arrancar WireMock') {
           steps {
                bat '''
                start /B java -jar D:\\UNIR\\wiremock-standalone-3.10.0.jar --port 8888
                '''
            }
        }

        stage('Ejecutar Wiremock') {
            steps {
                bat '''
                set MOCK_SERVER=http://localhost:8888
                set PYTHONPATH=%WORKSPACE%
                pytest --junitxml=result-Wire.xml test\\unit
                '''
            }
        }

        stage('Informe') {
            steps {
                //set PYTHONPATH=%WORKSPACE%
                junit 'result*.xml'
            }
        }

    }
}
