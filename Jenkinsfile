pipeline {
    agent any

    tools {
        maven 'MAVEN'
    }

    environment {
        APP_DIR = "/opt/springboot-app"
        JAR_NAME = "app.jar"
        BUILD_JAR = "target/demo-0.0.2-SNAPSHOT.jar"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scmGit(
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[url: 'https://github.com/danvisrinivas/demo.git']]
                )
            }
        }

        stage('Build Maven Project') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Deploy & Run on EC2') {
            steps {
                sh '''
                mkdir -p $APP_DIR

                # Stop old app if running
                pkill -f $JAR_NAME || true

                # Copy new jar
                cp $BUILD_JAR $APP_DIR/$JAR_NAME

                # Start app in background
                nohup java -jar $APP_DIR/$JAR_NAME > $APP_DIR/app.log 2>&1 &
                '''
            }
        }
    }
}


