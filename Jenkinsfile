pipeline {
    agent any

    parameters {
        choice(name: 'TEST_ENV', choices: ['QA', 'DEV', 'STAGE'], description: 'Choose the test environment')
    }

    environment {
        EMAIL_SUBJECT = "Booking API Test Report - ${env.JOB_NAME} #${env.BUILD_TAG}"
        RECIPIENTS = "tohfa.nay@gmail.com"
        THRESHOLD = 80
    }

    options {
        skipDefaultCheckout(true)
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/tohfanayDemo/BookingAPI.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }

        stage('Set Credentials for Selected Environment') {
            steps {
                script {
                    if (params.TEST_ENV == 'QA') {
                        env.CREDS_ID = 'qa-creds'
                    } else if (params.TEST_ENV == 'DEV') {
                        env.CREDS_ID = 'dev-creds'
                    } else if (params.TEST_ENV == 'STAGE') {
                        env.CREDS_ID = 'stage-creds'
                    }
                }
            }
        }

        stage('Run Newman Tests') {
            steps {
                script {
                    bat 'if not exist "%WORKSPACE%\\reports" mkdir "%WORKSPACE%\\reports"'

                    withCredentials([usernamePassword(credentialsId: "${env.CREDS_ID}", usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        bat """
                            newman run "%WORKSPACE%\\Postman Collections\\Booking.postman_collection.json" ^
                            -e "%WORKSPACE%\\Booking.postman_environment.json" ^
                            -d "%WORKSPACE%\\bookingAPI_testData.json" ^
                            -r cli,json,junit,html,htmlextra,allure ^
                            --reporter-json-export "%WORKSPACE%\\reports\\report.json" ^
                            --reporter-htmlextra-export "%WORKSPACE%\\reports\\htmlextra_report.html" ^
                            --reporter-htmlextra-includeAssets ^
                            --reporter-allure-export "%WORKSPACE%\\reports\\allure-results" ^
                            --disable-unicode ^
                            --env-var test_env=${params.TEST_ENV} ^
                            --env-var cmd_username=%USERNAME% ^
                            --env-var cmd_password=%PASSWORD% ^
                            || exit 0
                        """
                    }
                }
            }
        }

        stage('Evaluate Pass Threshold') {
            steps {
                script {
                    def report = readJSON file: "${env.WORKSPACE}\\reports\\report.json"
                    def total = report.run.stats.assertions.total
                    def failed = report.run.stats.assertions.failed
                    def passed = total - failed
                    def passRate = (passed / total) * 100

                    echo "Passed: ${passed} / ${total} (${passRate.toInteger()}%)"

                    if (passRate >= THRESHOLD.toInteger()) {
                        echo "Pass rate is above threshold (${THRESHOLD}%)"
                    } else {
                        currentBuild.result = 'FAILURE'
                        error("Pass rate (${passRate.toInteger()}%) is below threshold (${THRESHOLD}%)")
                    }
                }
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'reports/**/*', fingerprint: true

            publishHTML(target: [
                allowMissing: false,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: 'reports',
                reportFiles: 'htmlextra_report.html',
                reportName: 'Booking API Test Report'
            ])

            allure([
                includeProperties: false,
                jdk: '',
                reportBuildPolicy: 'ALWAYS',
                results: [[path: 'reports/allure-results']]
            ])

            emailext(
                subject: "${EMAIL_SUBJECT}",
                body: """Test execution completed.<br>
                         <b>Build:</b> <a href='${env.BUILD_URL}'>${env.BUILD_URL}</a><br>
                         <b>Result:</b> ${currentBuild.currentResult}<br><br>
                         See attached <b>HTML report</b> or <a href="${env.BUILD_URL}">view online</a>.""",
                to: "${RECIPIENTS}",
                mimeType: 'text/html',
                attachmentsPattern: 'reports/htmlextra_report.html',
                attachLog: true
            )
        }

        failure {
            mail to: "${RECIPIENTS}",
                 subject: "FAILED: ${EMAIL_SUBJECT}",
                 body: "The Booking API test pipeline failed. Check Jenkins for full logs."
        }
    }
}
