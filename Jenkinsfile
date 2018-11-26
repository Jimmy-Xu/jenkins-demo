node("jenkins-k8s") {
    stage('Clone') {
        echo "1.Clone Stage"
        sh 'bash -c "echo 192.30.253.113 github.com | sudo tee -a /etc/hosts"'
        environment {
            http_proxy = "http://192.168.64.1:8118"
            https_proxy = "http://192.168.64.1:8118"
        }
        sh 'ping -c 5 github.com'
        git url: "https://github.com/Jimmy-Xu/jenkins-demo.git"
        environment {
            http_proxy = ""
            https_proxy = ""
        }
    }
    stage('Test'){
        echo "2.Test Stage"
    }
    stage('Build') {
        echo "3.Build Docker Image Stage"
        sh "docker build -t xjimmyshcn/jenkins-demo:${build_tag} ."
    }
    stage('Push') {
        echo "4.Push Docker Image Stage"
        withCredentials([usernamePassword(credentialsId: 'dockerHub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
            sh "docker login -u ${dockerHubUser} -p ${dockerHubPassword}"

        }
        sh "docker push xjimmyshcn/jenkins-demo:${build_tag}"
    }
    stage('Deploy') {
        echo "5. Deploy Stage"
        def userInput = input(
            id: 'userInput',
            message: 'Choose a deploy environment',
            parameters: [
                [
                    $class: 'ChoiceParameterDefinition',
                    choices: "Dev\nQA\nProd",
                    name: 'Env'
                ]
            ]
        )
        echo "This is a deploy step to ${userInput}"
        sh "sed -i 's/<BUILD_TAG>/${build_tag}/' k8s.yaml"
        if (userInput == "Dev") {
            // deploy dev stuff
        } else if (userInput == "QA"){
            // deploy qa stuff
        } else {
            // deploy prod stuff
        }
        sh "kubectl apply -f k8s.yaml"
    }
}
