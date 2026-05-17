pipeline {
    agent {
        node {
            label "roboshop"
        }
    }
    environment {
        appVersion = ""
        ACC_ID = "911701613683"  // aws account id
        region = "us-east-1"
    }
    options {
        disableConcurrentBuilds()
        timeout(time: 5, unit: 'MINUTES')
    }
    // parameters {
    //     string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')
    //     text(name: 'BIOGRAPHY', defaultValue: '', description: 'Enter some information about the person')
    //     booleanParam(name: 'DEPLOY', defaultValue: false, description: 'Toggle this value')
    //     choice(name: 'CHOICE', choices: ['One', 'Two', 'Three'], description: 'Pick something')
    //     password(name: 'PASSWORD', defaultValue: 'SECRET', description: 'Enter a password')
    // }
    stages {
        stage('Read version') {
            steps {
                script {
                    // Load and parse the JSON file
                    def packageJson = readJSON file: 'package.json'
                    
                    // Access fields directly
                    appVersion = packageJson.version
                    echo "Building version ${appVersion}"
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                script{
                    sh """
                        npm install
                    """
                }
            }
        } 

        stage('Build Image') {
            steps {
               script{
                    withAWS(credentials: 'aws-creds', region: "${region}") {
                        // Commands here have AWS authentication
                        sh """
                            aws ecr get-login-password --region ${region} | \
                            docker login --username AWS --password-stdin ${ACC_ID}.dkr.ecr.${region}.amazonaws.com
                            docker build -t ${ACC_ID}.dkr.ecr.${region}.amazonaws.com/roboshop/catalogue:${appVersion} .
                            docker push ${ACC_ID}.dkr.ecr.${region}.amazonaws.com/roboshop/catalogue:${appVersion}
                        """
                    }
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    sh """
                        echo "Testing"
                        echo "Hello ${params.PERSON}"
                        echo "Biography: ${params.BIOGRAPHY}"
                        echo "Toggle: ${params.DEPLOY}"
                        echo "Choice: ${params.choice}"
                        echo "Password: ${params.PASSWORD}"
                    """
                }     
            }
        }
        stage('Deploy') {
            when {
                expression { "${params.DEPLOY}" == "true"}
            }
            // input {
            //     message "Should we continue?"
            //     ok "Yes, we should."
            //     submitter "alice,bob"
            //     parameters {
            //     string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')
            //     }
            // }
            steps {
                script {
                    sh """
                        echo "Deploying"
                    """
                }     
            }
        }
    }
    //post build
    post {
        always {
            echo "I will always say hello!"
        }
        success {
            echo "pipeline success"
        }
        failure {
            echo "pipeline failure"
        }
    }
}