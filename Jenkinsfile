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
                    don = sh(returnStdout: true, script: 'echo abc | grep a | cut -d" " -f2').trim()
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
                    // println "don is instanceof String, ${don instanceof String}"
                    println don.getClass()
                    if(don == '') {
                        println "don is empty string"
                    } else {
                        println "don is not empty string"
                        error 'Make the script step fail'
                    }

                    // test returnStatus of the sh step
                    rc = sh script: 'echo abc | grep d', returnStatus: true 
                    echo "Return status is ${rc}"
                    sh """echo 'Exit code is $rc'"""
                }

                sh 'echo foo will not be interpolated $foo'

                error 'Make the build stage fail'
                echo "This step will never be ran because of the above step"
            }

            post {
                failure {
                    echo 'Failed in stage test'
                }
            }
        }
    }
}
