pipeline {
    agent none
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'python:2-alpine'
                }
            }
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'qnib/pytest'
                }
            }
            steps {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
        stage('Deliver') {
            agent {
                docker {
                    image 'cdrx/pyinstaller-linux:python2'
                }
            }
            steps {
                sh 'pyinstaller --onefile sources/add2vals.py'
            }
            post {
                success {
                    archiveArtifacts 'dist/add2vals'
                }
            }
        }
        stage("Publish") {
           agent any
           steps {
               nexusArtifactUploader(
                   nexusVersion: 'nexus2',
                   protocol: 'http',
                   nexusUrl: 'ec2-18-218-242-18.us-east-2.compute.amazonaws.com:8081/nexus',
                   groupId: 'rtt.example',
                   version: '1.0',
                   repository: 'releases',
                   credentialsId: 'NexusDefault',
                   artifacts: [
                       [artifactId: 'add2vals',
                        classifier: '',
                        file: 'dist/add2vals',
                        type: '']
                   ]
               )
           }
        }
    }
}
