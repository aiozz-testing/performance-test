@Library('shared-library') _

// ================================
// CONFIGURATION
// ================================
TIMEZONE = 'GMT+7'
REPORT_URL = 'http://95.111.238.44:11000/'
// ================================
// JOB PIPELINE
// ================================
node {
    // get bot token and group chat id from credentials
    withCredentials([
        string(credentialsId: 'telegram-doremon-token', variable: 'telegramToken'),
        string(credentialsId: 'telegram-chat-id', variable: 'telegramChatId')
    ]) {
        env.CHAT_TOKEN = telegramToken
        env.CHAT_ID = telegramChatId
    }

    notifyBuild('STARTED')

    stage('Checkout code') {
        checkout scm
    }

    try {
        env.PASSED = 0;
        env.FAILED = 0;
        env.SKIPPED = 0;
        stage('Test') {
                sh "ls -l"
                sh "npx -y playwright install"
                sh "npm install @playwright/test"
                def status = sh(script: "npx playwright test UI Func CMS",returnStatus: true)
                if (status == 1) {
                    currentBuild.result = 'FAILURE'
                } else {
                    currentBuild.result = 'SUCCESS'
                }
                sh "ls -l"
                parseOutput()
                copyHtmlReport()
                notifyBuild(currentBuild.result)
        }
    } catch (Exception ex) {
        println(ex)
        currentBuild.result = 'FAILURE'
        notifyBuild('EXCEPTION', ex.message != null ? ex.message : ex.toString())
    }
}

// ================================
// HELPER FUNCTION
// ================================

def parseOutput(){
    def results = sh(script: "grep -E '\"expected\":|\"skipped\":|\"unexpected\":' results.json | awk '{print \$2}' | tr -d ',' | paste -sd ':'", returnStdout: true).trim()
    def resultsArr = results.split(":");
    env.PASSED = resultsArr[resultsArr.size()-3];
    env.SKIPPED = resultsArr[resultsArr.size()-2];
    env.FAILED = resultsArr[resultsArr.size()-1];
}

def copyHtmlReport(){
    sh "rm -rf ~/playwright-report/*"
    sh "mv playwright-report/* ~/playwright-report/"
}

def notifyBuild(buildStatus, exceptionErrorMessage = '') {
    def message = ''
    def jobName = escapeMarkdown("${env.JOB_NAME} - ${env.BUILD_NUMBER}")

    if (buildStatus == 'STARTED') {
        message = "➡️ *STARTED:* Job [${jobName}](${env.BUILD_URL})"
    } else if (buildStatus == 'EXCEPTION') {
        def errorHeader = "❌ *FAILURE:* Job [${jobName}](${env.BUILD_URL})"
        message = "${errorHeader}\n————————————————————\nJob is aborted due to exception error:\n*${escapeMarkdown(exceptionErrorMessage)}*"
    } else {
        def buildTime = formatMilisecondTime(timeInMillis: currentBuild.timeInMillis, timezone: TIMEZONE, format: 'MMM dd, yyyy HH:mm:ss Z')
        def duration = currentBuild.durationString.replace(' and counting', '')
        def buildTimeText = "*Running Time:* ${escapeMarkdown(buildTime)}"
        def durationText = "*Duration:* ${escapeMarkdown(duration)}"
        def reportLink = "[${escapeMarkdown("(view report)")}](${escapeMarkdown(REPORT_URL)})"
        def result = "*Result:* 🟢 ${env.PASSED} passed, 🔴 ${env.FAILED} failed, 🟠 ${env.SKIPPED} skipped ${reportLink}"
        if (buildStatus == 'SUCCESS') {
            def successHeader = " ✅ *SUCCESS:* Job [${jobName}](${env.BUILD_URL})"
            message = "${successHeader}\n————————————————————\n${buildTimeText}\n${durationText}\n${result}"
        } else {
            def errorHeader = "❌ *FAILURE:* Job [${jobName}](${env.BUILD_URL})"
            message = "${errorHeader}\n————————————————————\n${buildTimeText}\n${durationText}\n${result}"
        }
    }

    sendTelegramMessage(message: message, token: env.CHAT_TOKEN, chatID: env.CHAT_ID, mode: 'MarkdownV2')
}
