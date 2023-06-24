pipeline {

    agent none

    stages {

        stage('Checkout scm') {
            agent {
                docker {
                    image 'mingc/android-build-box'
                    reuseNode true
                }
            }
            steps {
                checkout scm
            }
        }

        stage('Build') {
            agent {
                docker {
                    image 'mingc/android-build-box'
                    args '--user root'
                    reuseNode true
                }
            }
            steps {
                sh 'jenv local 1.8'
                sh './gradlew clean assembleDebug --no-daemon'
            }
        }

        stage('OnFarmTest') {
            agent {
                label 'inbuilt'
            }
            environment {
                SD_URL = 'staging.smartdust.me'
                SD_TOKEN = 'true'
            }
            steps {
                sh './usr/local/bin/smartdust-client connect --min=2 -n 2 -f platform:Android'
                sh 'sleep 5'
                sh 'adb devices'
                sh './gradlew connectedAndroidTest'
            }
            post {
                always {
                    junit '**/build/outputs/**/debugAndroidTest/**'
                }
            }

        }

    }
}
