pipeline { 

    agent {
        docker {
            image "jenkins-agent-docker:latest"
            args '-v /var/run/docker.sock:/var/run/docker.sock'
            reuseNode true
            alwaysPull true
        }
    }
    
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
                    sh 'ls -lrt'
                }
            } 

        } 

        stage('Build') { 

            steps { 

                echo 'Building stage!' 

                sh 'make build' 

            } 

        } 

        stage('Unit tests') { 

            steps { 

                sh 'make test-unit' 

                archiveArtifacts artifacts: 'results/*.xml' 

            } 

        } 

    } 

    post { 

        always { 

            junit 'results/*_result.xml' 

            cleanWs() 

        } 

    } 

} 