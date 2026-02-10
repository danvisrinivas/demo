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
        cp $BUILD_JAR $APP_DIR/$JAR_NAME

        echo "================================="
        echo "Starting Spring Boot application"
        echo "================================="

        java -jar $APP_DIR/$JAR_NAME --server.port=9999 > $APP_DIR/app.log 2>&1 &

        APP_PID=$!
        echo "Started process with PID: $APP_PID"

        echo "Waiting for application to start..."
        sleep 15

        echo "Checking application status..."

        if ps -p $APP_PID > /dev/null
        then
            echo "✅ Application STARTED successfully"
        else
            echo "❌ Application FAILED to start"
            echo "===== Last 50 lines of log ====="
            tail -n 50 $APP_DIR/app.log
            exit 1
        fi
        '''
    }
}

    }
}




