// vi: ft=groovy

pipeline {
    agent any

    environment {
        manager = 'Jack'
    }

    stages {
        stage('build') {
            environment {
                staff = 'Peter'
            }

            steps {
                sh 'env'
                script {
                    // the following two lines have the same effect
                    foo = sh(returnStdout: true, script: "echo $env.staff").trim()
                    bar = sh(returnStdout: true, script: 'echo $staff').trim()
                    grep_result = sh(returnStdout: true, script: 'echo abc | grep a | cut -d" " -f2').trim()
                }
            }

            post {
                failure {
                    echo 'Failed in stage build'
                }
            }
        }

        stage('test') {
            when {
                allOf {
                    environment name: 'manager', value: 'Jack'
                    equals expected: 'Peter', actual: foo
                    expression { manager ==~ /J.+k/ }
                    expression { foo ==~ /[Pp]eter/ }
                    expression { bar == 'Peter' }
                }
            }
            steps {
                echo "Enter steps of the stage test"
                script {
                    println grep_result.getClass()
                    if(grep_result == '') {
                        echo "grep_result is empty string"
                        error 'Make the build stage fail'
                        echo "This step will never be ran because of the above step"
                    }

                    // test returnStatus of the sh step
                    rc = sh script: 'echo abc | grep d', returnStatus: true 
                    if(rc == 0) {
                        echo "Test the return status of the sh step, rc = $rc"
                    }
                }

                // local variables can not be interpolated in single quote
                sh 'echo foo=$foo will not be interpolated'

            }

            post {
                failure {
                    echo 'Failed in stage test'
                }
            }
        }
    }
}
