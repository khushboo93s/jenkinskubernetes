node {
    def app
     parameters {
        string(name: 'JENKINS_WORKSPACE', defaultValue: workspace, description: 'Jenkins WORKSPACE')
    }

    stage('Clone repository') {
      

        checkout scm
    }

    stage('Build image') {
  
       app = docker.build("khushboosingh93/test")
    }
     stage('environment variable') {
  

       sh 'echo $WORKSPACE'
    }


    stage('Test image') {
  

        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Push image') {
        
        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
            app.push("${env.BUILD_NUMBER}")
        }
    }
    
    stage('Update GIT') {
            script {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        //def encodedPassword = URLEncoder.encode("$GIT_PASSWORD",'UTF-8')
                        sh "git config user.email khushbooindu@gmail.com"
                        sh "git config user.name khushboo"
                        //sh "git switch master"
                        sh "cat deployment.yaml"
                        sh "sed -i 's+raj80dockerid/test.*+raj80dockerid/test:${env.BUILD_NUMBER}+g' deployment.yaml"
                        sh "cat deployment.yaml"
                        sh "git add ."
                        sh "git commit -m 'Triggered Build: ${env.BUILD_NUMBER}'"
                        sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/jenkinskubernetes.git HEAD:main"
      }
    }
  }
}
}
