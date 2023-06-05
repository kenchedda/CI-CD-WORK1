pipeline{
    agent{
        label "build"
    }
    tools {
        jdk 'Java17'
        maven 'maven3'
    }
   
     stages{
        stage("Cleanup Workspace"){
            steps {
                cleanWs()
            }

        }
    
        stage("Checkout from SCM"){
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/kenchedda/CI-CD-WORK1.git'
            }

         }

        stage("Build Application"){
            steps {
                sh "mvn clean package"
            }

        }

        stage("Test Application"){
            steps {
                sh "mvn test"
            }

        }  
          stage("Sonarqube Analysis") {
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'sonar') {
                        sh "mvn sonar:sonar"
                    }
                }
            }

        }

        stage("Quality Gate") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar'
                }
            }
        }
        stage('Docker image build'){
                    steps {
                        script {
                            sh 'docker image build -t $JOB_NAME:v1.$BUILD_ID .'
                            sh 'docker image tag $JOB_NAME:v1.$BUILD_ID kenappiah/$JOB_NAME:latest'
                        }
                    }
            
                }
            stage ('publish docker image') {
                steps{
                    script{
                        withCredentials([string(credentialsId: 'dockersec', variable: 'docker_hub_cred')]) {
                            sh 'docker login -u kenappiah -p ${docker_hub_cred}'
                            sh 'docker image push kenappiah/$JOB_NAME:latest1'
                    }
                }
            }                
        }

     

     }

}       


