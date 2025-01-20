pipeline {
    agent any
    parameters {
        choice(
            name: 'TEST_NAME',
            choices: [
                'PerformanceForLivestream',
                'PerformanceForRandomSwitchingPublicPrivateVideos',
                'PerformanceForVideo'
            ],
            description: 'Select the test you want to run'
        )
    }
    environment {
        JMETER_HOME = '/opt/apache-jmeter-5.6.3/bin'
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
                        rm -rf ./results/${params.TEST_NAME}Results.jtl ./reports/${params.TEST_NAME}Report &&
                        ${JMETER_HOME}/jmeter -n -t ${params.TEST_NAME}.jmx -l ./results/${params.TEST_NAME}Results.jtl -e -o ./reports/${params.TEST_NAME}Report
                    """
                }
            }
        }
        // stage('Publish Results') {
        //     steps {
        //         publishPerformanceTestResults parsers: [
        //             [$class: 'JMeterCsvParser', glob: '${params.TEST_NAME}Results.jtl']
        //         ]
        //     }
        // }
    }
}
