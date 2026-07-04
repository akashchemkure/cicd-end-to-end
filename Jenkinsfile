pipeline {
    
    agent any 
    
    environment {
        IMAGE_TAG = 1
    }
    
    stages {
        
        stage('Checkout'){
           steps {
                git credentialsId: 'github-credentials', 
                url: 'https://github.com/akashchemkure/cicd-end-to-end',
                branch: 'main'
           }
        }

        stage('Build Docker'){
            steps{
                script{
                    sh '''
                    echo 'Buid Docker Image'
                    docker build -t akashchemkure97/cicd-e2e:1 .
                    '''
                }
            }
        }

        stage('Push the artifacts'){
           steps{
                script{
                    sh '''
                    echo 'Push to Repo'
                    docker push akashchemkure97/cicd-e2e:1
                    '''
                }
            }
        }
        
        stage('Checkout K8S manifest SCM'){
            steps {
                git credentialsId: 'github-credentials', 
                url: 'https://github.com/akashchemkure/cicd-demo-manifests-repo.git',
                branch: 'main'
            }
        }
        
        stage('Update K8S manifest & push to Repo'){
            steps {
                script{
                    withCredentials([usernamePassword(credentialsId: 'github-credentials', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        sh """
                        cat deploy.yaml
                        sed -i "s/replaceImageTag/${BUILD_NUMBER}/g" deploy.yaml
                        cat deploy.yaml
                        git add deploy.yaml
                        git commit -m "Updated deploy yaml | Jenkins Pipeline"
                        git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/akashchemkure/cicd-demo-manifests-repo.git HEAD:main
                        """                       
                    }
                }
            }
        }
    }
}
