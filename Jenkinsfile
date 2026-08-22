pipeline {
  agent any
  
  tools{
    maven 'maven_3'
  }
  
  enviroments {
    GIT_REPO = 'GITOPS'
    GIT_REPO_URL = 'https://github.com/someshtarra/GITOPS.git'
    GIT_USER = 'someshtarra'
    IMAGE_NAME = 'someshtarra/project'
    SONAR_URL = ''
    DOCKER_USER = 'someshtarra'
  }

  stages{
    stage('check out') {
      steps{
        echo 'cloing code from git hub'
        git branch: 'main',
          url: 'https://github.com/someshtarra/project-management.git'
      }
    }

    stage('Code Scaner') {
      steps{
        echo 'code scaning using sonarqube'
        withCredentials([
          string(
            credentialsId: 'sonartoken',
            variable:'SONAR'
          )
        ]) {
        sh '''
        mvn org.sonarsource.scanner.maven:sonar-maven-plugin:sonar \
        -Dsonar.host.url=$SONAR_URL \
        -Dsonar.token=$SONAR
            '''
        }
      }
    }

    stage('Build Artifact') {
      steps {
        echo 'Building Arifact using maven'
        sh ' mvn clean package'
      }
    }

    stage('Building Docker image') {
      steps {
        echo 'Building image using Docker'
        sh 'docker build -t $IMAGE_NAME:${BUILD_NUMBER} -f Dockerfile .'
      }
    }

    stage('Image scan using trivy'){
      steps{
        echo 'Scaning image using Trivy'
        sh ' trivy image $IMAGE_NAME:${BUILD_NUMBER}
      }
    }

    stage('push To Dockerhub') {
      steps{
        withCredentials([
          string(
            credentialsId: 'docker_id',variable: 'DOCKER_PS'
          )
        ]) {
          sh 'echo 'DOCKER_PS' | docker login -u $DOCKER_USER --password-stdin '
          sh 'docker push $IMAGE_NAME:${BUILD_NUMBER}'
        }
      }
    }

    stage('Cloing GITOPS repo') {
      steps{
        echo 'cloing GITOPS repo'
        git branch: 'main',
          url: '$GIT_REPO_URL'
      }
    }
    stage('Updating deploy.yaml file'){
      steps{
        withCredentials([
          string(credentialsId: 'github_token', variable: 'GITHUB_TOKEN')
        ]) {
        sh''' 
        git config --global user.name "somesh"
        git config --gobal user.email "someshtarra@gmail.com"
        sed -i 's/$IMAGE_NAME:.*/$IMAGE_NAME:${BUILD_NUMBER}/g' Deployment/deploy.yaml
        git add .
        git commit -m 'updated image number ${BUILD_NUMBER}'
        git push  https://${GITHUB_TOKEN}@github.com/${GIT_USER}/${GIT_REPO}.git HEAD:main
        '''
        }
      }
    }
  }
}
  
