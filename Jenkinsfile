pipeline {

    agent none

    environment {
        ANDROID_SDK_ROOT = '/usr/lib/android-sdk'
    }

    stages {

        stage('Checkout scm') {
            agent {
                label 'build app node'
            }
            steps {
                checkout scm
            }
        }

        stage('Build') {
            agent {
                label 'build app node'
            }
            steps {
                echo 'build app'
                sh './gradlew clean assembleDebug --no-daemon'
                stash name: 'app', includes: '**', excludes: '**/.gradle/,**/.git/**'
            }
        }

        stage('OnFarmTest') {

            agent {
                label 'build test node'
            }
            environment {
                PATH = "/home/ubuntu/DevTools/stf-console-client-0.3.4/bin:$PATH"
            }
            steps {
                echo "PATH is: $PATH"
                echo 'test app'
                unstash 'app'
                sh 'pwd'
                sh 'stf'
                sh 'stf devices --all'
                sh 'sleep 5'
                sh 'stf connect --all &'
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
