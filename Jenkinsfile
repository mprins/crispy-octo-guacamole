timestamps {
    node {

        properties([
            [$class: 'jenkins.model.BuildDiscarderProperty', strategy: [$class: 'LogRotator',
                artifactDaysToKeepStr: '5',
                artifactNumToKeepStr: '5',
                daysToKeepStr: '15',
                numToKeepStr: '5']
            ]]);

        final def jdks = ['JDK8', 'OpenJDK11']

        stage("Prepare") {
             checkout scm
        }

        jdks.eachWithIndex { jdk, indexOfJdk ->
            final String jdkTestName = jdk.toString()

            withEnv(["JAVA_HOME=${ tool jdkTestName }", "PATH+MAVEN=${tool 'Maven 3.6.0'}/bin:${env.JAVA_HOME}/bin"]) {

                println "JDK: ${jdkTestName}"

                stage("Build: ${jdkTestName}") {
                    echo "Building branch: ${env.BRANCH_NAME}"
                    sh "mvn clean install -U -DskipTests -Dtest.skip.integrationtests=true -B -V -fae -q"
                }

                stage("Test: ${jdkTestName}") {
                    echo "Running unit tests"
                    sh "mvn -e clean test -B"
                }
            }
        }
    }
}
