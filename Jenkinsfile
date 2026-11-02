node {
    def appDir = "${env.HOME}/Desktop/nextjs-app"

    stage('Clean Workspace') {
        echo 'Cleaning Jenkins Workspace'
        deleteDir()
    }

    stage('Clone Repo') {
        echo 'Cloning the repository'

        git(
            branch: 'main',
            url: 'https://github.com/Taniya635/CICE-Jenkins-AWS.git'
        )
    }

    stage('Deploy Application') {
        echo 'Deploying Next.js application'

        sh """
            set -e

            mkdir -p "${appDir}"

            rsync -av --delete \
                --exclude='.git' \
                --exclude='node_modules' \
                ./ "${appDir}/"

            cd "${appDir}"

            npm install
            npm run build

            PID=\$(lsof -ti tcp:3000 || true)

            if [ -n "\$PID" ]; then
                echo "Stopping existing application..."
                kill \$PID
            fi

            echo "Starting Next.js application..."

            nohup npm run start > app.log 2>&1 < /dev/null &
            
            sleep 5

            echo "Application deployment completed."
        """
    }
}
