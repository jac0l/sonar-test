pipeline {
    agent any

    environment {
        SONAR_HOST_URL = 'http://sonarqube:9000'
        SONAR_TOKEN = credentials('sonar-token-id')
        DEPENDENCY_CHECK_PATH = '/opt/dependency-check/bin/dependency-check.sh'
        SCAN_PATH = 'src'  
        OUTPUT_DIR = '/tmp'  
    }

    stages {
        stage('Checkout External Project') {
            steps {
                git branch: 'main',
                    credentialsId: 'jac0l',
                    url: 'git@github.com:jac0l/sonar-test.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    withSonarQubeEnv('SonarQube') {
                        sh '/opt/sonar-scanner/bin/sonar-scanner'
                    }
                }
            }
        }


      stage('Quality Gate') {
            steps {
                script {
                    def qg = waitForQualityGate()
                    if (qg.status != 'OK') { error "Pipeline aborted due to quality gate failure: ${qg.status}"
                        
                    }
                }
            }
        }
     stage('Updateonly') {
            steps {
                script {
                    sh """
                    ${DEPENDENCY_CHECK_PATH} --updateonly --data /var/jenkins_home/workspace/
                    """
                }
            }
        }            
       stage('Dependency Check') {
            steps {
                script {
                    sh """
                    ${DEPENDENCY_CHECK_PATH} -s ${SCAN_PATH} --noupdate --data /var/jenkins_home/workspace/
                    """
                }
            }
        }
    }
}

