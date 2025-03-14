// @Library('Shared')_
// pipeline{
//     agent { label 'dev-server'}
    
//     stages{
//         stage("Code clone"){
//             steps{
//                 sh "whoami"
//             clone("https://github.com/LondheShubham153/django-notes-app.git","main")
//             }
//         }
//         stage("Code Build"){
//             steps{
//             dockerbuild("notes-app","latest")
//             }
//         }
//         stage("Push to DockerHub"){
//             steps{
//                 dockerpush("dockerHubCreds","notes-app","latest")
//             }
//         }
//         stage("Deploy"){
//             steps{
//                 deploy()
//             }
//         }
        
//     }
// }
pipeline {
    
    agent { label "shabari" }
    
    stages {
        stage("Cloning"){
            steps{
                sh "whoami"
                echo "Cloning code"
                git url: "https://github.com/medashabari/django-notes-app.git", branch: 'main'
                echo "Code clone successfully completed"
            }
        }
        stage("Build"){
            steps{
                sh "docker build -t shabarimeda03/django-notes-app:${env.BUILD_NUMBER} ."
            }
        }
        stage("Upload docker image"){
            steps{
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'dockerHubPass', usernameVariable: 'dockerHubUser')]) {
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    sh "docker push ${env.dockerHubUser}/django-notes-app:${env.BUILD_NUMBER}"
                }
            }
        }
    stage("Deploy") {
            steps {
                script {
                    def previousBuild = env.BUILD_NUMBER.toInteger() - 1
                    echo "Previous Build: ${previousBuild}"
                    echo "Current Build: ${env.BUILD_NUMBER}"
        
                    // Replace IMAGE_TAG with previous build number
                    sh "sed -i 's/IMAGE_TAG/${previousBuild}/g' docker-compose.yml"
        
                    // Handle errors when stopping containers
                    try {
                        sh "docker compose down || true" // Continue even if it fails
                    } catch (Exception e) {
                        echo "Warning: docker compose down failed, but continuing with deployment."
                    }
        
                    // Replace IMAGE_TAG with latest
                    sh "sed -i 's/IMAGE_TAG/latest/g' docker-compose.yml"
        
                    // Handle errors when stopping containers
                    try {
                        sh "docker compose down || true" // Continue even if it fails
                    } catch (Exception e) {
                        echo "Warning: docker compose down failed, but continuing with deployment."
                    }
        
                    // Replace IMAGE_TAG with the current build number
                    sh "sed -i 's/IMAGE_TAG/${env.BUILD_NUMBER}/g' docker-compose.yml"
        
                    // Start the new container
                    sh "docker compose up -d"
                }
            }
        }
    }
}
