pipeline {
    agent any

    stages {

        stage('Clone Code') {
            // Pull the latest code from GitHub
            steps {
                git branch: 'main', 
                    url: 'https://github.com/SyedAwwab/devops-pipeline-demo.git'
            }
        }

        stage('SonarQube Analysis') {
            // Scan code for bugs, vulnerabilities, code smells
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh '''
                        sonar-scanner \
                        -Dsonar.projectKey=myapp \
                        -Dsonar.sources=. \
                        -Dsonar.host.url=http://localhost:9000
                    '''
                }
            }
        }

        stage('OWASP Dependency Check') {
            // Scan libraries for known security vulnerabilities
            steps {
                dependencyCheck additionalArguments: '--scan ./ --format ALL', 
                                odcInstallation: 'OWASP-DC'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }

        stage('Docker Build') {
            // Package the app into a Docker container
            steps {
                sh 'docker build -t myapp:latest .'
            }
        }

        stage('Deploy') {
            // Stop old container if running, start new one
            steps {
                sh '''
                    docker stop myapp || true
                    docker rm myapp || true
                    docker run -d --name myapp -p 80:5000 myapp:latest
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed! App is live.'
        }
        failure {
            echo 'Something failed. Check the stage logs above.'
        }
    }
}
