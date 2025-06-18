pipeline {
    agent any

    environment {
        // Thư mục chứa file .csproj của Backend
        BACKEND_DIR = 'backend/MMartHandMade/MMartHandMade'
        CSPROJ_FILE = 'MMartHandMade.csproj'

        // Thư mục chứa frontend tĩnh (HTML/CSS/JS)
        FRONTEND_DIR = 'frontend'

        // Script khởi tạo CSDL (nếu có)
        DB_SCRIPT = 'database\\init.sql'

        // Thư mục đích cho IIS
        FRONTEND_DEST = 'C:\\wwwroot\\HandMadeMart_UI'
        BACKEND_DEST  = 'C:\\wwwroot\\HandMadeMart_API'
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
                    bat "dotnet restore ${CSPROJ_FILE}"
                    bat "dotnet build ${CSPROJ_FILE} --configuration Release"
                    bat "dotnet publish ${CSPROJ_FILE} -c Release -o ../../../../publish-api"
                }
            }
        }

        stage('Copy Frontend to IIS') {
            steps {
                echo '🌐 Deploying frontend to IIS...'
                bat """
                    iisreset /stop
                    if exist "${FRONTEND_DEST}" rd /S /Q "${FRONTEND_DEST}"
                    mkdir "${FRONTEND_DEST}"
                    xcopy "${FRONTEND_DIR}\\*" "${FRONTEND_DEST}" /E /I /Y
                """
            }
        }

        stage('Copy Backend API to IIS') {
            steps {
                echo '🚀 Deploying backend API to IIS...'
                bat """
                    if exist "${BACKEND_DEST}" rd /S /Q "${BACKEND_DEST}"
                    mkdir "${BACKEND_DEST}"
                    xcopy "publish-api\\*" "${BACKEND_DEST}" /E /I /Y
                    iisreset /start
                """
            }
        }

        stage('(Optional) Init Database') {
            steps {
                echo '🗃️ Running database init script...'
                powershell """
                    if (Test-Path '${DB_SCRIPT}') {
                        sqlcmd -S .\\SQLEXPRESS -i '${DB_SCRIPT}'
                    } else {
                        Write-Output '⚠️ SQL script not found, skipping database init.'
                    }
                """
            }
        }

        stage('✅ Deployment Complete') {
            steps {
                echo '🎉 CI/CD pipeline completed successfully!'
            }
        }
    }
}
