pipeline {
    agent any

    tools {
        maven 'MAVEN_HOME'
        jdk 'jdk21'
    }

    environment {
        DEPLOY_PATH = 'F:\\Servers\\apache-tomcat-10.1.28\\webapps'
        APP_NAME    = 'demo'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/pradeepahirwarjul92/springboot-ci-cd.git'
            }
        }

        stage('Build WAR') {
            steps {
                bat 'mvn clean package'
            }
        }

        stage('Remove Old Deployment') {
            steps {
                bat """
                if exist "%DEPLOY_PATH%\\%APP_NAME%" (
                    rmdir /s /q "%DEPLOY_PATH%\\%APP_NAME%"
                )

                if exist "%DEPLOY_PATH%\\%APP_NAME%.war" (
                    del /f /q "%DEPLOY_PATH%\\%APP_NAME%.war"
                )
                """
            }
        }

        stage('Deploy WAR') {
            steps {
                bat """
                copy target\\%APP_NAME%.war %DEPLOY_PATH%
                """
            }
        }

        stage('Health Check') {
    steps {
        powershell """
        Start-Sleep -Seconds 15

        \$url = "http://localhost:8090/${APP_NAME}/api/hello"

        Write-Host "Checking URL: \$url"

        try {
            \$response = Invoke-WebRequest -Uri \$url -UseBasicParsing

            Write-Host "Application Running Successfully"
            Write-Host "HTTP Status Code: \$response.StatusCode"
        }
        catch {
            Write-Host "Health Check Failed"
            throw
        }
        """
    }
}
    }

    post {
        success {
            echo 'Deployment Successful'
        }

        failure {
            echo 'Deployment Failed'
        }
    }
}