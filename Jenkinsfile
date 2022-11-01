

pipeline {
    agent { label 'container' }
    environment {
        dockerhub=credentials('Docker_Hub')
    }
    stages {
        stage('Build & Deploy') {
     steps {
                script {
                    if (env.BRANCH_NAME == 'master') {
                        sh """
                        docker login -u $dockerhub_USR -p $dockerhub_PSW
                        docker build -t mohab5897/app:$BUILD_NUMBER .
                        docker push mohab5897/app:$BUILD_NUMBER
                        echo ${BUILD_NUMBER} > ../build
                        """
                } else if (env.BRANCH_NAME == 'stage' || env.BRANCH_NAME == 'dev' || env.BRANCH_NAME == 'test') {
                withCredentials([file(credentialsId: 'config', variable: 'cfg')]){
                        sh """
                        if [-f build]; then
                            export BUILD_NUMBER=\$(cat ../build)
                        else
                            export BUILD_NUMBER=0
                        fi
                        mv Deployment/deploy.yaml Deployment/deploy
                        cat Deployment/deploy | envsubst > Deployment/deploy.yaml
                        rm -f Deployment/deploy
                        kubectl apply --kubeconfig=${cfg} -f Deployment/
                        """
                 }
                } 

                }
            }
            // post {
            //     success {
            //         slackSend (channel: 'jenkins-multibranch', color: '#00FF00', message: "BUILD STAGE SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'[${env.BRANCH_NAME}]")

            //     }
            //     failure {
            //         slackSend (channel: 'jenkins-multibranch', color: '#FF0000', message: "BUILD STAGE FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' [${env.BRANCH_NAME}]")
            //     }
            // }
        }
        // stage('Push BackHouse') {
        //     steps {
        //         sh 'docker tag back_house:v1 mohamedalaaelsafy/app:v1'
        //         sh 'docker push mohamedalaaelsafy/app:v1'
        //     }
        
            // post {
            //     success {
            //         slackSend (channel: 'jenkins-multibranch', color: '#00FF00', message: "BUILD STAGE SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' [${env.BRANCH_NAME}]")

            //     }
            //     failure {
            //         slackSend (channel: 'jenkins-multibranch', color: '#FF0000', message: "BUILD STAGE FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'[${env.BRANCH_NAME}]")
            //     }
            // }
        // }

        // stage('Deploy BackHouse') {
        //     steps {
        //         sh 'kubectl apply -f Deployment/service.yaml --validate=false'
        //         sh 'kubectl apply -f Deployment/Deploy.yaml --validate=false'
        //     }
        
            // post {
            //     success {
            //         slackSend (channel: 'jenkins-multibranch', color: '#00FF00', message: "BUILD STAGE SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' [${env.BRANCH_NAME}]")

            //     }
            //     failure {
            //         slackSend (channel: 'jenkins-multibranch', color: '#FF0000', message: "BUILD STAGE FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'[${env.BRANCH_NAME}]")
            //     }
            // }
        // }
    }

    post {
        success {
            echo 'This will run only if successful'
        }
    }
}
