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
                    reuseNode true
                }
            }
            steps {
                echo 'build app'
                sh './gradlew clean assembleDebug --no-daemon'
                stash name: 'app', includes: '**', excludes: '**/.gradle/,**/.git/**'
            }
        }

        stage('OnFarmTest') {

            agent {
                label 'inbuilt'
            }
            steps {
                echo 'test app'
                unstash 'app'
                sh 'pwd'
                sh 'stf-client -t 8a51b13fe1ee46bf97ca6bd659319644684bf4ce2a5d4d21958c648f0b72b23f -u https://sebastian.smartdust.me connect --all'
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
