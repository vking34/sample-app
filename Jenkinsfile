node('jenkins-slave') {
    
     stage('test pipeline') {
        sh(script: """
        echo "hello"
        git clone https://github.com/vking34/sample-app.git
        cd ./sample-app/
        
        docker build . -t sample-app:2.0
        echo "done!"
        """)
    }
}