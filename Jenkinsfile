pipeline {

    agent none

    environment {
        ANDROID_SDK_ROOT = '/usr/lib/android-sdk'
    }

    stages {

        stage('Checkout scm') {
            agent {
                label 'THIS'
            }
            steps {
                checkout scm
            }
        }

        stage('Build') {
            agent {
                label 'THIS'
            }
            steps {
                echo 'build app'
                sh './gradlew clean assembleDebug --no-daemon'
                stash name: 'app', includes: '**', excludes: '**/.gradle/,**/.git/**'
            }
        }

        stage('OnFarmTest') {

            agent {
                label 'THIS'
            }
	    environment {
		BUNDLE_GEMFILE = '/home/jordan/workspace/stf-client'
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
                    junit '**/debugAndroidTest/**'
                }
            }

        }

    }
}
