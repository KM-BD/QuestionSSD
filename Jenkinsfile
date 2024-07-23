pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/KM-BD/QuestionSSD.git'
            }
        }
         
        stage('Code Quality Check via SonarQube') { 
           steps { 
               script { 
                def scannerHome = tool 'SonarQube'; 
                   withSonarQubeEnv('SonarQube') { 
                   sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=SSD2116 -Dsonar.sources=." 
                   } 
               } 
           } 
        } 
        stage('Install Dependencies') {
            steps {
                sh 'pip install -r webapp/requirements.txt'
            }
        }

        stage('Run Unit Tests') {
            steps {
                sh 'pytest webapp/tests/unit'
            }
            post {
                always {
                    junit 'webapp/tests/unit/reports/*.xml'
                }
            }
        }

        stage('OWASP Dependency Check') {
            steps {
                dependencyCheck additionalArguments: '--noupdate', odcInstallation: 'DependencyCheck'
            }
            post {
                always {
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
                }
            }
        }

        stage('Run Integration Tests') {
            steps {
                sh 'pytest webapp/tests/integration'
            }
            post {
                always {
                    junit 'webapp/tests/integration/reports/*.xml'
                }
            }
        }

        stage('Run UI Tests') {
            steps {
                sh 'pytest webapp/tests/ui'
            }
            post {
                always {
                    junit 'webapp/tests/ui/reports/*.xml'
                }
            }
        }
    }

    // post {
    //     always {
    //         sh 'docker-compose down'
    //     }
    // }
}
