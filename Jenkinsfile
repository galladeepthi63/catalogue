pipeline {
    agent {
        node {
            label 'AGENT-1'
            
        }
    }
    environment { 
        packageVersion = ''
        nexusURL = '172.31.5.46:8081'
    }
    options {
        timeout(time: 1, unit: 'HOURS') 
        disableConcurrentBuilds()
    }
    parameters {
    //     string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')

    //     text(name: 'BIOGRAPHY', defaultValue: '', description: 'Enter some information about the person')

        booleanParam(name: 'Deploy', defaultValue: false, description: 'Toggle this value')

    //     choice(name: 'CHOICE', choices: ['One', 'Two', 'Three'], description: 'Pick something')

    //     password(name: 'PASSWORD', defaultValue: 'SECRET', description: 'Enter a password')
     }
    stages {
        stage('Get the version') {
            steps {
                //
                script {
                    def packagejson = readJSON file: 'package.json'
                    packageVersion = packagejson.version
                    echo "application version :$packageVersion"
                }
                
            }
        }
        stage('install dependencies') {
            steps {
                //
                sh """
                npm install 
                """
            }
        }
         stage('Unit tests') {
            steps {
                sh """
                    echo "unit tests will run here"
                """
            }
        }

        stage('Sonar Scan'){
            steps{
                sh """
                    sonar-scanner
                """
            }
        }
        stage('build') {
            steps {
                //
                sh """
                    ls -la
                    zip -q -r catalogue.zip ./* -x ".git" -x "*.zip"
                    ls -ltr
                """
            }
        }
        stage('Publish Artifact') {
            steps {
                 nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: "${nexusURL}",
                    groupId: 'com.roboshop',
                    version: "${packageVersion}",
                    repository: 'catalogue',
                    credentialsId: 'nexus-auth',
                    artifacts: [
                        [artifactId: 'catalogue',
                        classifier: '',
                        file: 'catalogue.zip',
                        type: 'zip']
                    ]
                )
            }
        }
        stage('Deploy') {
              when {
                expression{
                    params.Deploy == 'true'
                }
            }
            steps {
                //
                script {
                    def params = [
                            string(name: 'version', value: "$packageVersion"),
                            string(name: 'environment', value: "dev")
                        ]
                        build job: "catalague-deploy", wait: true,parameters: params
                }
                
            }
        }
        // stage('check param') {
        //     steps {
        //         sh """
        //             echo "Hello ${params.PERSON}"

        //             echo "Biography: ${params.BIOGRAPHY}"

        //             echo "Toggle: ${params.TOGGLE}"

        //             echo "Choice: ${params.CHOICE}"

        //             echo "Password: ${params.PASSWORD}"
        //         """
        //     }
        // }
    }
    // post build
    post { 
        always { 
            echo 'I will always say Hello again!'
            deleteDir()
        }
        failure { 
            echo 'This run when pipeline failed ,get the failed alert!'
        }
          success { 
            echo 'This run when pipeline success ,say success!'
        }

    }
}