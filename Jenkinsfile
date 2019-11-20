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
        string(name:'REPORT_MAIL',
               defaultValue: 'jesus.maneiro.figueroa@gmail.com')
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
            when{
                branch 'master'
            }
            steps {
                sh 'mvn deploy'
                sh 'tar -cvzf deploy-artifact.tar.gz deploy/'
                archiveArtifacts artifacts: 'deploy-artifact.tar.gz', fingerprint: true
            }
        }
        stage('Benchmark') {
            steps {
                script {
                    try {
                        timeout(time: 2, unit: 'MINUTES') {
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
            sh 'tar -cvzf reports.tar.gz reports'
            emailext (attachmentsPattern: 'reports.tar.gz',
                body: "Workflow result on ${currentBuild.currentResult}, check attached artifacts for further information",
                subject: "Jenkins Build ${currentBuild.currentResult} on Job ${env.JOB_NAME}",
                from: 'notificaciones.torusnewies@gmail.com',
                replyTo: '',
                to: "${params.REPORT_MAIL}"
            )
            archiveArtifacts artifacts: 'reports.tar.gz', fingerprint: true
        }
        success {
            script {
                if (${env.BRANCH_NAME} == 'master')
                    archiveArtifacts artifacts: 'deploy-artifact.tar.gz', fingerprint: true
            }
        }
        cleanup{
            cleanWs()
        }
    }
}
