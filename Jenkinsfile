

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
                withCredentials([file(credentialsId: 'cfg', variable: 'cfg')]){
                        sh """
                            export BUILD_NUMBER=\$(cat ../build)
                       
                        mv Deployment/deploy.yaml Deployment/deploy
                        cat Deployment/deploy | envsubst > Deployment/deploy.yaml
                        rm -f Deployment/deploy
                        kubectl apply --kubeconfig=${cfg} -f Deployment/
                        """
                 }
                } 

                }
            }
//         pipeline {
//     agent { label 'new' }
//     stages {
//         stage('build') {
//             steps {
//                 script {
//                     if ( env.BRANCH_NAME == "release" ) {
//                     withCredentials([usernamePassword(credentialsId: "dockerhub" , usernameVariable: 'USER' , passwordVariable: 'PASS')]){
//                         sh """
//                             docker login -u ${USER} -p ${PASS}
//                             docker build -t xxxxxx/new:${BUILD_NUMBER} .
//                             docker push fathalla22/new:${BUILD_NUMBER}
//                             echo ${BUILD_NUMBER} > ../build.txt
//                            """
//                     }
//                     }
//                 }
//             }
//         }
//         stage('deploy') {
//             steps {
//                 script {
//                     if ( env.BRANCH_NAME == "dev" || env.BRANCH_NAME == "test" || env.BRANCH_NAME == "prod" ) {
//                       withCredentials([file(credentialsId: "kubernetes" , variable: 'CONFIG' )]){
//                         sh """
//                             export BUILD_NUMBER=\$(cat ../build.txt)
//                             cat Deployment/deploy.yaml | envsubst > Deployment/deploy.tmp
//                             mv Deployment/deploy.tmp Deployment/deploy.yaml
//                             rm -f Deployment/deploy.yaml.tmp
//                             kubectl apply -f Deployment --kubeconfig=${CONFIG}
//                            """
//                       }
//                 }
//                 }
//             }
//         }
//     }
// }

    post {
        success {
            echo 'This will run only if successful'
        }
    }
}
