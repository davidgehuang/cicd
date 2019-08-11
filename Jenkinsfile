node {
   //For public
   def registry = "reg.davidge.cn"
   def project = "cicd"
   def app_name = "java_web"
   def project_path = "java-web/java-demo-aliang"
   def k8_file = "deploy.yml"
   def k8_deploy = "k8s-java-demo.sh"
   def image_name = "${registry}/${project}/${app_name}:${BUILD_NUMBER}"
   def git_address = "git@github.com:davidgehuang/cicd.git"
   // Auth
   def secret_name = "registry-pull-secret"
   def docker_registry_auth = "1304d523-e737-4dce-baa7-67ffbea15d7f"
   def git_auth = "4a13f301-b60e-4d91-b436-51c4906650ec"
   //def k8s_auth = '1a4e1ae1-e59b-4c60-80ba-53f40bfd211d'
   //def k8s_auth = "23418072-2141-473a-9781-41154c0318b9"
   
   stage('Preparation') {
     checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: "${git_auth}", url: "${git_address}"]]])
      echo 'git sucessed'
   }
   stage('Maven package') {
      sh "cd ${project_path} && mvn clean package"
      echo 'maven successed'
   }
   stage('Build Image'){
      withCredentials([usernamePassword(credentialsId: "${docker_registry_auth}", passwordVariable: 'password', usernameVariable: 'username')]) {
            sh """
              echo '
                FROM reg.davidge.cn/k8s/java-demo 
                RUN rm -rf /usr/local/tomcat/webapps/*
                ADD java-web/java-demo-aliang/target/*.war /usr/local/tomcat/webapps/ROOT.war
              ' > Dockerfile
              
              docker build -t ${image_name} .
              docker login -u ${username} -p '${password}' ${registry}
              docker push ${image_name}
            """
            echo "Build Image successed"
            }
      }
   stage('Deploy to k8s'){
             sh """
                sed -i 's@\$IMAGE_NAME@${image_name}@g' ${project_path}/${k8_file}
                sed -i 's@\$SECRET_NAME@${secret_name}@g' ${project_path}/${k8_file}
             """  
            sh """
            echo "
                #!/bin/bash
                cd ${project_path} &&
                kubectl version
                sleep 1
                kubectl get pod
                sleep 1
                kubectl apply -f ${k8_file}
                echo "apply ok"
                cat ${k8_file}
              " > ${k8_deploy}
                 """
            sh "chmod 755 ${k8_deploy} && ./${k8_deploy}"
            echo "Deploy successed"
        }
 }
