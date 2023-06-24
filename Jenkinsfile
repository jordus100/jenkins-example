pipeline {

    agent none

    stages {

        stage('Checkout scm') {
            agent {
                docker {
                    image 'mingc/android-build-box-smartdust'
                }
            }
            steps {
                checkout scm
            }
        }

        stage('Build') {
            agent {
                docker {
                    image 'mingc/android-build-box-smartdust'
                    args '--user root'
                }
            }
            steps {
                sh 'jenv local 1.8'
                sh './gradlew clean assembleDebug --no-daemon'
            }
        }

        stage('OnFarmTest') {
            agent {
                docker {
                    image 'mingc/android-build-box-smartdust'
                    args '--user root'
                }
            }
            environment {
                SD_URL = 'https://staging.smartdust.me'
                SD_TOKEN = 'a534c80c572442689dd560c4bc34921ce441781b34434f5bb02b062424a89fee'
                ANDROID_SDK_ROOT = '/opt/android-sdk'
            }
            steps {
                sh 'smartdust-client connect --min=2 -n 2 -f platform:Android'
                sh 'sleep 5'
                sh 'adb devices'
                sh './gradlew connectedAndroidTest'
                sh 'smartdust-client disconnect --all'
            }
            post {
                always {
                    junit '**/build/outputs/**/debugAndroidTest/**'
                }
            }

        }

    }
}
