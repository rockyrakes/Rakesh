FROM node:20 AS build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM nginx:stable-alpine
COPY --from=build /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]



call npm ci || call npm install
call npm run build
mkdir "C:\ProgramData\Jenkins\.jenkins\userContent\freestyle\"
xcopy /E /I /Y "C:\ProgramData\Jenkins\.jenkins\workspace\freestyle\dist\*" "C:\ProgramData\Jenkins\.jenkins\userContent\freestyle\"



pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/akash012345678/MovieTicketBookingSystem.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                dir('movie-ticket-booking') {
                    bat '"C:\\Program Files\\nodejs\\npm.cmd" install'
                }
            }
        }

        stage('Build') {
            steps {
                dir('movie-ticket-booking') {
                    bat '"C:\\Program Files\\nodejs\\npm.cmd" run build'
                }
            }
        }
    }

    post {
        success {
            echo "React project built successfully!"
        }
        failure {
            echo "Build failed!"
        }
    }
}
