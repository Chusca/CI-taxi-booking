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
        string(name:'BENCHMARK_SCRIPT',
               defaultValue: 'https://gist.githubusercontent.com/Chusca/9a75b4c3926768588fcfbb60f5753463/raw/benchmark.sh')
    } 
    
    stages {
        stage('Setup') {
            steps {
                git branch: "${params.APP_GIT_BRANCH}", url: "${env.GIT_REPO_APP}"
                sh 'mkdir reports'
                sh 'apt update'
                sh 'apt install bc'
                sh 'apt clean'
            }
        }
        stage('Validate') {
            steps {
                sh 'mvn validate > reports/validate.txt'
            }
        }
        stage('Compile') {
            steps {
                sh 'mvn compile > reports/compile.txt'
            }
        }
        stage('Tests') {
            steps {
                sh 'mvn test > reports/test.txt'
                sh 'find . -type f -regex ".*/target/.*/TEST.*\\.xml" -exec cp {} reports \\;'
            }
        }
        stage('Package') {
            steps {
                sh 'mvn package > reports/package.txt'
            }
        }
        stage('Verify') {
            steps {
                sh 'mvn verify > reports/verify.txt'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploy'
            }
        }
        stage('Benchmark') {
            steps {
                script {
                    try {
                        timeout(time: 2, unit: 'MINUTE') {
                            sh "curl -o benchmark.sh -s ${params.BENCHMARK_SCRIPT}"
                            sh 'bash benchmark.sh 3'
                        }
                    } catch (err) {
                        // This try catch prevents Jenkins from setting currentBuild to
                        // ABORTED in case of benchmark failure
                        writeFile(file: "reports/benchmark_report.txt",
                                text: "Benchmarks too slow", encoding: "UTF-8")
                    }
                }
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
