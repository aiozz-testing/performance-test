pipeline {
    agent any
    environment {
        JMETER_HOME = '/root/apache-jmeter-5.6.3/bin'
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
                    sh "${JMETER_HOME}/rm -rf ./results/PerformanceForLivestreamResults.jtl ./reports/PerformanceForLivestreamReport && ./jmeter -n -t PerformanceForLivestream.jmx -l ./results/PerformanceForLivestreamResults.jtl -e -o ./reports/PerformanceForLivestreamReport"
                }
            }
        }
        // stage('Publish Results') {
        //     steps {
        //         publishPerformanceTestResults parsers: [
        //             [$class: 'JMeterCsvParser', glob: 'PerformanceForLivestreamResults.jtl']
        //         ]
        //     }
        // }
    }
}
