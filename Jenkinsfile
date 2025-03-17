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
                echo "Cloning code"
                git url: "https://github.com/medashabari/django-notes-app.git", branch: 'main'
                echo "Code clone successfully completed"
            }
        }
        stage("Build"){
            steps{
                sh "docker build -t shabarimeda03/django-notes-app:latest ."
            }
        }
        stage("Upload docker image"){
            steps{
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'dockerHubPass', usernameVariable: 'dockerHubUser')]) {
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    sh "docker push ${env.dockerHubUser}/django-notes-app:latest"
                }
            }
        }
    stage("Deploy") {
            steps {
            sh "docker compose down && docker compose up -d"
            }
        }
    }
}
