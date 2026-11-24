pipeline {

    agent {
        label 'AGENT-1'
    }

    options {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
    }

    environment {
        appVersion = ''
        component = 'catalogue'
        project = 'roboshop'
        region = 'us-east-1'
        acc_id = '150450879000'
    }

    stages {
        stage('Read package.json'){
            steps {
                script{
                    def packageJson = readJSON file: package.json
                    appVersion = packageJson.version
                    echo "Package version is: ${appVersion}"
                }
            }
        }

        stage('install dependencies'){
            steps {
                script{
                    sh """
                        npm install
                    """
                }
            }
        }
        stage('docker build'){
            steps {
                script{
                    withAWS(credentials: '', region: 'us-east-1')
                        sh """
                            aws ecr get-login-password --region ${region} | docker login --username AWS --password-stdin ${acc_id}.dkr.ecr.${region}.amazonaws.com
                            docker build -t ${acc_id}.dkr.ecr.${region}.amazonaws.com/${project}/${component}:${appVersion} .
                            docker push ${acc_id}.dkr.ecr.${region}.amazonaws.com/${project}/${component}:${appVersion}
                        """
                }
            }
        }
    }

     post { 
        always { 
            echo 'I will always say Hello again!'
            deleteDir()
        }
        success { 
            echo 'Hello Success'
        }
        failure { 
            echo 'Hello Failure'
        }
    }


}