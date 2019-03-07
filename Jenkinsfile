node {

    checkout scm

    env.DOCKER_API_VERSION="1.23"
    
    sh "git rev-parse --short HEAD > commit-id"

    tag = readFile('commit-id').replace("\n", "").replace("\r", "")
    appName = "hello-kenzan"
    registryHost = "127.0.0.1:30400/"
    imageName = "${registryHost}${appName}:${tag}"
    env.BUILDIMG=imageName

    stage "Build"
    
        sh "docker build -t ${imageName} -f applications/hello-kenzan/Dockerfile applications/hello-kenzan"
    
    stage "Push"

        sh "docker push ${imageName}"
	
    stage "Deploy"
	
        sh'''#!/bin/bash -xe
              echo 'Replasing string with docker image neme within deployment.yaml file'
              "sed -i 's/127.0.0.1:30400/hello-kenzan:$BUILD_TAG' applications/${appName}/k8s/deployment.yaml"
          ''' 
        kubernetesDeploy configs: "applications/${appName}/k8s/deployment.yaml", kubeconfigId: 'kenzan_kubeconfig' 
}

