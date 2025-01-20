pipeline {
    agent any
    environment {
        JMETER_HOME = '/opt/apache-jmeter-5.6.3/bin'
        // TEST_NAME = 'PerformanceForLivestream'
        TEST_NAME = 'PerformanceForRandomSwitchingPublicPrivateVideos'
        // TEST_NAME = 'PerformanceForVideo'
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/aiozz-testing/performance-test.git'
            }
        }
        stage('Run JMeter Tests') {
            steps {
                script {
                    sh """
                        rm -rf ./results/${TEST_NAME}Results.jtl ./reports/${TEST_NAME}Report &&
                        ${JMETER_HOME}/jmeter -n -t ${TEST_NAME}.jmx -l ./results/${TEST_NAME}Results.jtl -e -o ./reports/${TEST_NAME}Report
                    """
                }
            }
        }
        // stage('Publish Results') {
        //     steps {
        //         publishPerformanceTestResults parsers: [
        //             [$class: 'JMeterCsvParser', glob: '${TEST_NAME}Results.jtl']
        //         ]
        //     }
        // }
    }
}
