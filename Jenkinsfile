pipeline {
    agent {
        docker {
            image 'maven:3.6.0-jdk-8'
        }
    }
    stages {
        stage('Setup') {
            steps {
                echo 'Setup'
            }
        }
        stage('Validate') {
            steps {
                echo 'Validate'
            }
        }
        stage('Compile') {
            steps {
                echo 'Compile'
            }
        }
        stage('Tests') {
            steps {
                echo 'Tests'
            }
        }
        stage('Package') {
            steps {
                echo 'Package'
            }
        }
        stage('Verify') {
            steps {
                echo 'Verify'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploy'
            }
        }
        stage('Benchmark') {
            steps {
                echo 'Benchmark'
            }
        }
    }
    post {
        always {
            echo 'always'
        }
        failure {
            echo 'failure'
        }
        success {
            echo 'success'
        }
        cleanup{
            cleanWs()
        }
    }
}
