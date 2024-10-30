pipeline {
    agent {
        label 'ubuntu'
    }
    options {
        skipStagesAfterUnstable()
    }
    environment {
         MYPATH="/home/slawek/.local/bin"
    }
    stages {
        stage('Build') {
            steps {
                echo "Running ${env.BUILD_ID} on ${env.JENKINS_URL}"
                sh 'python3 -m py_compile sources/add2vals.py sources/calc.py'
                stash(name: 'compiled-results', includes: 'sources/*.py*')
            }
        }
        stage('Test') {
            steps {
                sh "${MYPATH}/py.test --junit-xml test-reports/results.xml sources/test_calc.py"
            }
            post {
                always {
                    junit skipPublishingChecks: true, testResults:'test-reports/results.xml'
                }
            }
        }
        stage('Deliver') { 
            steps {
                sh "${MYPATH}/pyinstaller --onefile sources/add2vals.py" 
            }
            post {
                success {
                    archiveArtifacts 'dist/add2vals' 
                }
            }
        }
    }
}
