pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'http://localhost:3000/repository.git'
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

        stage('Deploy Application') {
            steps {
                sh 'docker-compose up -d'
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
