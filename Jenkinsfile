pipeline {
    agent any
    parameters {
        string(name: 'THREAD_COUNT', defaultValue: '1', description: 'Number of threads (users) to simulate')
        string(name: 'RAMP_UP_TIME', defaultValue: '1', description: 'Ramp-up time in seconds')
        string(name: 'LOOP_COUNT', defaultValue: '1', description: 'Number of iterations per thread')
        string(name: 'LIVESTREAM_ID', defaultValue: '0', description: 'Livestream id')

        choice(
            name: 'TEST_NAME',
            choices: [
                'PerformanceForLivestream',
                'PerformanceForRandomSwitchingPublicPrivateVideos',
                'PerformanceForVideo'
            ],
            description: 'Select the test you want to run'
        )

        choice(
            name: 'Env',
            choices: [
                'w3stream.attoaioz.cyou',
                'w3stream.xyz'
            ],
            description: 'Select the environment to test against'
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
                        ${JMETER_HOME}/jmeter -n -t ${params.TEST_NAME}.jmx \
                            -l ./results/${params.TEST_NAME}Results.jtl \
                            -e -o ./reports/${params.TEST_NAME}Report \
                            -JTHREAD_COUNT=${params.THREAD_COUNT} \
                            -JRAMP_UP_TIME=${params.RAMP_UP_TIME} \
                            -JLOOP_COUNT=${params.LOOP_COUNT} \
                            -JLIVESTREAM_ID=${params.LIVESTREAM_ID}
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
