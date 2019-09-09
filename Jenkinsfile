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
                    image 'python:2-alpine'
                }
            }
            steps {
                sh 'ls -la sources/add2vals.py'
            }
            post {
                success {
                    archiveArtifacts 'sources/add2vals.py'
                }
            }
        }
        stage("Publish") {
           agent any
           steps {
               nexusArtifactUploader(
                   nexusVersion: 'nexus3',
                   protocol: 'http',
                   nexusUrl: 'nexus.roundtower.io:8081',
                   groupId: 'apps',
                   version: '1.0',
                   repository: 'training6-snapshot',
                   credentialsId: 'NexusDefault',
                   artifacts: [
                       [artifactId: 'add2vals',
                        classifier: '',
                        file: 'sources/add2vals.py',
                        type: '']
                   ]
               )
           }
        }
    }
}
