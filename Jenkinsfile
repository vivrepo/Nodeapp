pipeline{
    agent any

    environment{
        EC2_HOST = ""
        SSH_CREDENTIAL_ID = ""
        REMOTE_USER = "ubuntu"
        REMOTE_PATH = "/home/ubuntu/app"
        WEB_ROOT = "/var/www/html" 
        SERVER = "nginx"
    }

    stages{
        stage("Build"){
            step{
                echo "Installing Dependencies"
                sh "npm install"
                echo "Building the app"
                sh "npm run build"
                echo "Build Complete"
            } 
        }
        stage("Deploy"){
            step{
                echo "Starting Deployment"
                sshagent(credential: [env.SSH_CREDENTIAL_ID]){
                    sh"""
                        echo "Creating Remote Directory"
                        ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${EC2_HOST} 'mkdir -p ${REMOTE_PATH}'

                        echo "Copying Distribution to Remote Path"
                        scp -o StrictHostKeyChecking=no -r dist/* ${REMOTE_USER}@${EC2_HOST}:${REMOTE_PATH}/

                        echo "Restarting Nginx"
                        sshssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${EC2_HOST}'
                            sudo rm -rf ${WEB_ROOT}/*
                            sudo cp -r ${REMOTE_PATH}/* ${WEB_ROOT}/
                            sudo sytemctl restart ${SERVER}
                        '
                    """
                }
            }
        }
    }
}