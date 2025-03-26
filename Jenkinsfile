pipeline{
    agent any
    tools{
        maven 'Maven_3.2.5'   //tools block is case sensitive
    }
    environment{
        SONAR_TOKEN=credentials('sonar-token')
    }
    stages{
        stage('Compile and run sonar analysis'){
            steps{
                sh '''
                    mvn clean verify sonar:sonar \
                    -Dsonar.projectKey=devsecops19 \
                    -Dsonar.organization=devsecops19 \
                    -Dsonar.host.url=https://sonarcloud.io \
                    -Dsonar.login=$SONAR_TOKEN
                '''
            }
        }


        // stage('Use Snyk') {
        //     steps {
        //         // Load credential into a variable (masked)
        //         withCredentials([string(credentialsId: 'snyk-cred', variable: 'SNYK_TOKEN')]) {
        //             sh 'mvn snyk:test -Dsnyk.token=$SNYK_TOKEN'  // $SNYK_TOKEN is securely injected
        //         }
        //     }
        // }

        stage('Building Docker Image'){
            steps{
                script{
                    app=docker.build("008971657113.dkr.ecr.ap-south-1.amazonaws.com/devsecops/devsecops/buggyapp:latest")
                }
            }
        }

        stage('Pushing docker image to ECR/registry'){
            steps{
                script{
                    docker.withRegistry('https://008971657113.dkr.ecr.ap-south-1.amazonaws.com/devsecops','cloud_credentials'){
                        app.push('latest')
                        app.push("${env.build_number}")
                    }
                }
            }
        }
    }	
    post{
        success{
            echo "Pipeline succeeded"
        }
        failure{
            echo "pipeline failed"
        }
    }
}
