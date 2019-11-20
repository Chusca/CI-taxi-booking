pipeline {
    agent {
        docker {
            image 'maven:3.6.0-jdk-8'
        }
    }

    parameters{
        string(name:'GIT_REPO_APP',
               defaultValue:'https://github.com/sebascm/taxi-booking.git')
        string(name:'APP_GIT_BRANCH',
               defaultValue: "master")
    } 
    
    stages {
        stage('Setup') {
            steps {
                git branch: "${params.APP_GIT_BRANCH}", url: "${env.GIT_REPO_APP}"
                sh 'mkdir reports'
            }
        }
        stage('Validate') {
            steps {
                sh 'mvn validate > reports/validate.txt'
            }
        }
        stage('Compile') {
            steps {
                echo 'mvn compile > reports/compile.txt'
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
