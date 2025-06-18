pipeline {
    agent any

    environment {
        BACKEND_DIR = 'backend'
        FRONTEND_DIR = 'frontend'
        DB_SCRIPT = 'database\\init.sql'
        FRONTEND_DEST = 'C:\\wwwroot\\HandMadeMart_UI'
        BACKEND_DEST = 'C:\\wwwroot\\HandMadeMart_API'
    }

    stages {

        stage('Clone Source Code') {
            steps {
                echo '📥 Cloning source code...'
                git branch: 'main', url: 'https://github.com/ZuyAnhIT/HandMadeMart.git'
            }
        }

        stage('Build Backend API') {
            steps {
                dir("${BACKEND_DIR}") {
                    echo '🔧 Restoring and Building ASP.NET API...'
                    bat 'dotnet restore'
                    bat 'dotnet build --configuration Release'
                    bat 'dotnet publish -c Release -o ../publish-api'
                }
            }
        }

        stage('Copy Frontend to IIS') {
            steps {
                echo '🌐 Deploying frontend to IIS...'
                bat '''
                    iisreset /stop
                    rd /S /Q "%FRONTEND_DEST%"
                    mkdir "%FRONTEND_DEST%"
                    xcopy "%FRONTEND_DIR%\\*" "%FRONTEND_DEST%" /E /I /Y
                '''
            }
        }

        stage('Copy Backend API to IIS') {
            steps {
                echo '🚀 Deploying backend API to IIS...'
                bat '''
                    rd /S /Q "%BACKEND_DEST%"
                    mkdir "%BACKEND_DEST%"
                    xcopy "publish-api\\*" "%BACKEND_DEST%" /E /I /Y
                    iisreset /start
                '''
            }
        }

        stage('(Optional) Init Database') {
            steps {
                echo '🗃️ Running database init script...'
                powershell '''
                    sqlcmd -S .\\SQLEXPRESS -i database\\init.sql
                '''
            }
        }

        stage('✅ Deployment Complete') {
            steps {
                echo '🎉 CI/CD pipeline completed successfully!'
            }
        }
    }
}
