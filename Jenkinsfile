
node {
    def appDir = "${env.HOME}/nextjs-app"

    stage('Clean Workspace') {
        echo 'Cleaning Jenkins workspace'
        deleteDir()
    }

    stage('Clone Repository') {
        echo 'Cloning latest code from GitHub'

        git(
            branch: 'main',
            url: 'https://github.com/Taniya635/CICE-Jenkins-AWS.git'
        )
    }

    stage('Deploy Application') {
        timeout(time: 20, unit: 'MINUTES') {
            echo 'Starting deployment'

            sh """
                set -eux

                echo "Checking Node and npm"
                node --version
                npm --version

                mkdir -p '${appDir}'

                rsync -av --delete \
                    --exclude='.git' \
                    --exclude='node_modules' \
                    ./ '${appDir}/'

                cd '${appDir}'

                echo "Installing dependencies"
                npm install

                echo "Building Next.js application"
                npm run build

                echo "Stopping previous application"
                PID=\$(lsof -ti tcp:3000 || true)

                if [ -n "\$PID" ]; then
                    kill \$PID
                    sleep 2
                fi

                echo "Starting application"
                nohup npm run start > app.log 2>&1 < /dev/null &

                sleep 5
                cat app.log

                echo "Deployment completed"
            """
        }
    }
}
