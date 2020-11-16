pipeline {
    agent none
    stages {
        stage('build and test') {
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
                            echo "Do Prepare for ${PLATFORM} - ${BROWSER}"
                            checkout scm
                        }
                    }
                    stage('Build') {
                        steps {
                            echo "Do Build for ${JDK} - ${DATABASE}"
                            sh "mvn clean install -U -DskipTests -Dtest.skip.integrationtests=true -B -V -fae -q"
                            sh "mvn -e clean test -B"
                        }
                    }
                    stage('Test') {
                        steps {
                            echo "Do Test for ${JDK} - ${DATABASE}"
                            sh "mvn -e clean test -B"
                        }
                    }
                }
            }
        }
    }
}
