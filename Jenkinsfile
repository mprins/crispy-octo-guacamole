pipeline {
    agent none
    options {
        timeout(time: 1, unit: 'HOURS') 
        timestamps ()
    }
    stages {
        stage('build and test') {
                tools {
                    maven 'Maven CURRENT'
                    jdk "${JDK}"
                }
            matrix {
                agent any
                axes {
                    axis {
                        name 'JDK'
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
                            echo "Do Prepare for ${JDK} - ${DATABASE}"
                        }
                    }
                    stage('Build') {
                        steps {
                            echo "Do Build for ${JDK} - ${DATABASE}"
                            sh "mvn clean install -U -DskipTests -Dtest.skip.integrationtests=true -B -V -fae -q"
                        }
                    }
                    stage('Test') {
                        steps {
                            echo "Do Test for ${JDK} - ${DATABASE}"
                            sh "mvn -e clean test -B"
                        }
                        post {
                            success {
                                echo "Testing ${JDK} - ${DATABASE} passed"
                            }
                            failure {
                                echo "Testing ${JDK} - ${DATABASE} failed"
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
