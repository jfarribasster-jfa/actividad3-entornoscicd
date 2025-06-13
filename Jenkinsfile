pipeline { 

    agent any
    
    stages { 

        stage('Checkout Code') { 

            steps { 
                script { 
                    echo 'Checking out code...'
                    checkout ([
                        $class: 'GitSCM',
                        branches: [[name: "*/master"]],
                        userRemoteConfigs: [[url: 'https://github.com/srayuso/unir-cicd.git', credentialsId: 'GitHubUser']],
                        extensions: [[$class: 'CleanBeforeCheckout']]
                    ])
                }
            } 

        } 

        stage('Build') { 

            steps { 
                echo 'Building stage!' 
                sh 'make build' 
            } 

        } 

        stage('Fix Makefile Port Conflict') {
            steps {
                sh '''
                    # Reemplaza -p 5000:5000 por -p 5050:5000 solo en la línea relevante
                    sed -i.bak 's/-p 5000:5000/-p 5050:5000/' Makefile
                '''
            }
        }

        stage('Unit tests') { 

            steps { 

                sh 'make test-unit' 

                archiveArtifacts artifacts: 'results/*.xml' 

            } 

        } 

        stage('Test API') { 
            steps { 
                // Eliminar contenedor anterior si existe
                sh 'docker rm -f apiserver || true'
                // Eliminar red anterior si existe
                sh 'docker network rm calc-test-api || true'
                sh 'make test-api' 
                archiveArtifacts artifacts: 'results/*.xml' 

            } 

        } 
        stage('Test E2E') { 

            steps { 

                sh 'make test-e2e' 
                archiveArtifacts artifacts: 'results/*.xml' 

            } 

        }
    } 

    post { 

        always { 

            junit 'results/*_result.xml' 
            publishHTML([
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: 'results/coverage',
                    reportFiles: 'index.html',
                    reportName: 'Coverage Report'
                ])
            cleanWs() 

        } 

    } 

} 