pipeline {
    agent {
        label 'ubuntu-22.04' 
    }
    environment {
        APACHE_LOG = '/var/log/apache2/access.log'
    }
    stages {
        stage('Preparation') {
            steps {
                sh 'sudo apt update'
            }
        }
        stage('Install Apache2') {
            steps {
                script {
                    try {
                        sh '''
                            sudo apt install -y apache2
                            sudo systemctl start apache2
                            sudo systemctl enable apache2
                        '''
                    } catch (Exception e) {
                        error "Failed to install Apache2: ${e}"
                    }
                }
            }
        }
        stage('Verify Apache2') {
            steps {
                script {
                    def status = sh(script: 'curl -s -o /dev/null -w "%{http_code}" http://localhost', returnStdout: true).trim()
                    if (status != '200') {
                        error "Apache2 is not running properly. HTTP Status: ${status}"
                    } else {
                        echo "Apache2 is running. HTTP Status: ${status}"
                    }
                }
            }
        }
        stage('Check Apache2 Logs') {
            steps {
                script {
                    def logErrors = sh(script: "sudo cat ${APACHE_LOG} | grep -E '\" 4[0-9]{2} |\" 5[0-9]{2} ' || true", returnStdout: true).trim()
                    if (logErrors) {
                        echo "Found errors in logs:\n${logErrors}"
                    } else {
                        echo "No 4xx or 5xx errors found in ${APACHE_LOG}"
                    }
                }
            }
        }
    }
    post {
        always {
            sh 'sudo systemctl stop apache2 || true'
            echo 'Cleaning up...'
        }
        failure {
            echo 'Pipeline failed. Check logs for details.'
        }
        success {
            echo 'Pipeline completed successfully!'
        }
    }
}
