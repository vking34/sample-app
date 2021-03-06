node('jenkins-slave') {

    git_info = checkout scm

    // sh "echo ${git_info.GIT_COMMIT}"

    sh "git rev-parse --short HEAD > commit-id"
    tag = readFile('commit-id').replace("\n", "").replace("\r", "")

    stage('Build') {
        // Build docker image
        sh (script: """
        # cd ./ci-cd/sample-app/
        docker build . -t vking34/sample-app:${tag}
        """)

        docker.withRegistry("", "dockerhub"){
            sh "docker push vking34/sample-app:${tag}"
        }

        // Mail
        mail bcc: '', body: 'Success!', cc: '', from: '', replyTo: '', subject: '[sample-app](Build)', to: 'lzzvkingzzl@gmail.com'
    }
    
    stage('Test') {
        // Test here
        sh(script: """
        echo "Done!"
        """)

        // Mail
        mail bcc: '', body: 'Success!', cc: '', from: '', replyTo: '', subject: '[sample-app](Test)', to: 'lzzvkingzzl@gmail.com'
    }

    stage('Delivery to Staging'){
        // Clone and edit image:tag in staging directory then push
        withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
            sh(script: """
            cd ../
            git clone https://github.com/vking34/sample-app-deployments.git
            cd ./sample-app-deployments
            git config --global user.name "vking34"
            git config --global user.email "lzzvkingzzl@gmail.com"
            sed -i -z -E "s/sample-app:[^\\n]+\\n/sample-app:${tag}\\n/g" staging/deployments/deployment.yaml
            git add .
            git commit -m \"fix(staging): Delivery to staging env with commit ${tag}\"
            git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/vking34/sample-app-deployments.git
            """)
        }
        
        // Mail
        mail bcc: '', body: 'Success!', cc: '', from: '', replyTo: '', subject: '[sample-app](Delivery)', to: 'lzzvkingzzl@gmail.com'
    }

    stage('Deploy to Prod'){
        // Ask for approvement then edit image:tag in prod directory then push
        // input message:'Approve deployment?'
        withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
            sh(script: """
            cd ../sample-app-deployments
            pwd
            ls -la
            git config --global user.name "vking34"
            git config --global user.email "lzzvkingzzl@gmail.com"
            sed -i -z -E "s/sample-app:[^\\n]+\\n/sample-app:${tag}\\n/g" prod/deployments/deployment.yaml
            git add .
            git commit -m \"fix(prod): Delivery to staging env with commit ${tag}\"
            git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/vking34/sample-app-deployments.git
            """)
        }

        // Mail
        mail bcc: '', body: 'Success!', cc: '', from: '', replyTo: '', subject: '[sample-app](Deploy)', to: 'lzzvkingzzl@gmail.com'
    }
}