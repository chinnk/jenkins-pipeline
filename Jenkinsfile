pipeline {
    agent any
    stages {
        stage('Install Apache2') {
            steps {
                sh '''
                    sudo apt update
                    sudo apt install -y apache2
                    sudo systemctl start apache2
                    sudo systemctl enable apache2
                '''
            }
        }
        stage('Verify Apache2') {
            steps {
                sh 'curl -I http://localhost'
            }
        }
        stage('Check Apache2 Logs') {
            steps {
                sh '''
                    sudo cat /var/log/apache2/access.log | grep -E '" 4[0-9]{2} |" 5[0-9]{2} ' || echo "No 4xx or 5xx errors found"
                '''
            }
        }
    }
}
