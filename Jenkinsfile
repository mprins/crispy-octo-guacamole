timestamps {

    properties([
        [$class: 'jenkins.model.BuildDiscarderProperty', strategy: [$class: 'LogRotator',
            artifactDaysToKeepStr: '8',
            artifactNumToKeepStr: '3',
            daysToKeepStr: '15',
            numToKeepStr: '5']
        ]]);

    final def jdks = ['OpenJDK8']

    node {
        stage('Prepare') {
            sh "ulimit -a"
            sh "free -m"
            sh "whoami"
            sh "pwd"
            sh "echo $HOME"
            checkout scm
        }

        jdks.eachWithIndex { jdk, indexOfJdk ->
            final String TEST_JDK_NAME = jdk.toString()

            withEnv(["JAVA_HOME=${ tool TEST_JDK_NAME }", "PATH+MAVEN=${tool 'Maven CURRENT'}/bin:${env.JAVA_HOME}/bin"]) {

                stage('Settings') {
                    sh "mvn -V help:effective-settings"
                }

                stage('Build') {
                    echo "Building branch: ${env.BRANCH_NAME}"
                    sh "mvn clean package -Dmaven.test.skip=true -B -V -e -fae -q"
                }

                stage('Test') {
                    echo "Running unit tests"
                    sh "mvn -e test -B"
                }

                if (TEST_JDK_NAME == 'OpenJDK11') {
                    stage("cleanup Java 11 packages") {
                        echo "Verwijder de Java 11 build artifacts uit lokale repo"
                        sh "mvn build-helper:remove-project-artifact"
                    }
                }
            }
        }

        withEnv(["JAVA_HOME=${ tool 'OpenJDK8' }", "PATH+MAVEN=${tool 'Maven CURRENT'}/bin:${env.JAVA_HOME}/bin"]) {
            stage('Publish Test Results') {
                junit allowEmptyResults: true, testResults: '**/target/surefire-reports/TEST-*.xml, **/target/failsafe-reports/TEST-*.xml'
            }

            stage('Test Coverage results') {
                jacoco exclusionPattern: '**/*Test.class', execPattern: '**/target/**.exec'
            }

            stage('OWASP Dependency Check') {
                echo "Uitvoeren OWASP dependency check"
                sh "mvn org.owasp:dependency-check-maven:check"
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml', failedNewCritical: 1, failedNewHigh: 1, failedTotalCritical: 1, failedTotalHigh: 3, unstableTotalHigh: 2
            }
        }
    }
}
