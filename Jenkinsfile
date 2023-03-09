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
		BUNDLE_GEMFILE = '/home/jordan/workspace/stf-client/Gemfile'
	    }
            steps {
                echo 'test app'
                unstash 'app'
                sh 'pwd'
                sh 'bundle exec stf-client -t 6f837a14355149739df5ee83879799223e06ae8267f14749820b283c52c3e799 -u https://sebastian.smartdust.me connect --all'
                sh 'sleep 5'
                sh 'adb devices'
                sh './gradlew connectedAndroidTest --no-daemon'

            }
            post {
                always {
                    junit '**/debugAndroidTest/**'
                }
            }

        }

    }
}
