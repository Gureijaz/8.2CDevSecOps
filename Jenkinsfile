// SIT223/753 - 7.1C Part 1 Task 2 (DevSecOps Basics) + Part 2 Task 2 (Email Notifications)
//
// Runs npm-based security testing over the nodejs-goof app, and emails a
// build-status notification with the console log attached at the end of the
// Run Tests and NPM Audit (Security Scan) stages.
//
// Requires the "Email Extension Plugin" configured under
// Manage Jenkins > Configure System > Extended E-mail Notification.
//
// Jenkins here runs on Windows, so 'bat' is used instead of 'sh', and
// '|| exit /b 0' replaces '|| true' so the pipeline can continue past
// expected non-zero exit codes (failing tests / audit findings).
pipeline {
    agent any

    environment {
        NOTIFY_RECIPIENT = 'gureijazsinghaulakh@gmail.com'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Gureijaz/8.2CDevSecOps.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                bat 'npm test || exit /b 0'
            }
            post {
                always {
                    emailext(
                        subject: "[Run Tests] ${currentBuild.currentResult}: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                        body: """<p>Stage: <b>Run Tests</b></p>
                                 <p>Result: <b>${currentBuild.currentResult}</b></p>
                                 <p>Job: ${env.JOB_NAME} #${env.BUILD_NUMBER}</p>
                                 <p>Console: <a href="${env.BUILD_URL}console">${env.BUILD_URL}console</a></p>
                                 <p>Full console log is attached.</p>""",
                        mimeType: 'text/html',
                        to: "${env.NOTIFY_RECIPIENT}",
                        attachLog: true
                    )
                }
            }
        }

        stage('Generate Coverage Report') {
            steps {
                bat 'npm run coverage || exit /b 0'
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                bat 'npm audit || exit /b 0'
            }
            post {
                always {
                    emailext(
                        subject: "[Security Scan] ${currentBuild.currentResult}: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                        body: """<p>Stage: <b>NPM Audit (Security Scan)</b></p>
                                 <p>Result: <b>${currentBuild.currentResult}</b></p>
                                 <p>Job: ${env.JOB_NAME} #${env.BUILD_NUMBER}</p>
                                 <p>Console: <a href="${env.BUILD_URL}console">${env.BUILD_URL}console</a></p>
                                 <p>npm audit findings (known CVEs) are in the attached console log.</p>""",
                        mimeType: 'text/html',
                        to: "${env.NOTIFY_RECIPIENT}",
                        attachLog: true
                    )
                }
            }
        }
    }
}
