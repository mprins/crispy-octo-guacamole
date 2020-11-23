pipeline {
    agent none
    options {
        timeout(time: 1, unit: 'HOURS') 
        timestamps ()
    }
    tools {
        maven 'Maven CURRENT'
    }
    stages {
        stage('build and test') {
            matrix {
                agent any
                axes {
                    axis {
                        name 'JAVA'
                        values 'JDK8', 'OpenJDK11'
                    }
                    axis {
                        name 'DATABASE'
                        values 'PostgreSQL', 'Oracle', 'MSSQL'
                    }
                }
                stages {
                    stage('Prepare') {
                        steps {
                            echo "Do Prepare for ${JAVA} - ${DATABASE}"
                        }
                    }
                    stage('Build') {
                        tools {
                            jdk "${JAVA}"
                            maven 'Maven OLD'
                        }
                        steps {
                            // withEnv(["JAVA_HOME=${tool ${JAVA}}", "PATH=${tool ${JAVA}}/bin:${env.PATH}"]) {
                                echo "Do Build for ${JAVA} - ${DATABASE}"
                                sh "mvn clean install -U -DskipTests -Dtest.skip.integrationtests=true -B -V -fae -q"
                            // }
                        }
                    }
                    stage('Test') {
                        tools {
                            jdk "${JAVA}"
                        }
                        steps {
                            echo "Do Test for ${JAVA} - ${DATABASE}"
                            sh "mvn -e clean test -B"
                        }
                        post {
                            success {
                                echo "Testing ${JAVA} - ${DATABASE} passed"
                            }
                            failure {
                                echo "Testing ${JAVA} - ${DATABASE} failed"
                            }
                            always {
                                junit 'target/surefire-reports/*.xml'
                            }
                        }
                    }
                }
            }
        }
    }
}
