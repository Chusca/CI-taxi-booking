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
                sh 'mvn validate | tee reports/validate.txt'
            }
        }
        stage('Compile') {
            steps {
                sh 'mvn compile | tee reports/compile.txt'
            }
        }
        stage('Tests') {
            steps {
                sh 'mvn test | tee reports/test.txt'
                sh 'find . -type f -regex ".*/target/.*/TEST.*\\.xml" -exec cp {} reports \\;'
            }
        }
        if (("${params.APP_GIT_BRANCH}" == 'dev')||("${params.APP_GIT_BRANCH}" == 'master')){
            stage ("Lanzar pipeline_testing"){
                steps {
                    build job: 'pipeline_testing',
                        propagate: false,
                        wait: false,
                        parameters: [[$class: 'StringParameterValue',
                                    name: 'APP_ARTIFACT',
                                    value: "${APP_ARTIFACT}"],
                                    [$class: 'StringParameterValue',
                                    name: 'ENV_ARTIFACT',
                                    value: "${ENV_ARTIFACT}"]]
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
        cleanup{
            cleanWs()
        }
    }
}
