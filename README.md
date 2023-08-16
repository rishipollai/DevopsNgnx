# DevopsNgnx
Jenkins Pipeline
=================================================================================
pipeline {
    agent any

    stages {
        stage('Git checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/rishipollai/DevopsNgnx.git']])
            }
        }
        
        stage('Docker build and deploy image for nodejs') {
            steps {
                sh "docker build -t nodeimg ."
                sh "docker run -d -p 8810:8888 --name nodejsserver nodeimg"
            }
       }
       stage('Deploy docker image through nginx reverse proxy') {
            steps {
                sh 'cd ngnx/ && docker build -t ngnximg .'
                sh 'docker run -d -p 80:80 --link nodejsserver --name nginxcont ngnximg'
            }
       }
    }
        
}
===============================================================================================


Jenkins Logfiles
===============================================================================


Started by user admin
[Pipeline] Start of Pipeline
[Pipeline] node
Running on Jenkins in /var/lib/jenkins/workspace/pipline_demo
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Git checkout)
[Pipeline] checkout
The recommended git tool is: NONE
No credentials specified
 > git rev-parse --resolve-git-dir /var/lib/jenkins/workspace/pipline_demo/.git # timeout=10
Fetching changes from the remote Git repository
 > git config remote.origin.url https://github.com/rishipollai/DevopsNgnx.git # timeout=10
Fetching upstream changes from https://github.com/rishipollai/DevopsNgnx.git
 > git --version # timeout=10
 > git --version # 'git version 2.34.1'
 > git fetch --tags --force --progress -- https://github.com/rishipollai/DevopsNgnx.git +refs/heads/*:refs/remotes/origin/* # timeout=10
 > git rev-parse refs/remotes/origin/master^{commit} # timeout=10
Checking out Revision c86690a5ce6170769901d33c4c9ad47138315744 (refs/remotes/origin/master)
 > git config core.sparsecheckout # timeout=10
 > git checkout -f c86690a5ce6170769901d33c4c9ad47138315744 # timeout=10
Commit message: "nginx conf done"
 > git rev-list --no-walk c86690a5ce6170769901d33c4c9ad47138315744 # timeout=10
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Docker build and deploy image for nodejs)
[Pipeline] sh
+ docker build -t nodeimg .
Sending build context to Docker daemon  9.602MB

Step 1/6 : FROM node:14
 ---> 1d12470fa662
Step 2/6 : WORKDIR /usr/src/app
 ---> Using cache
 ---> 3dbe2dc5fc2a
Step 3/6 : RUN npm install
 ---> Using cache
 ---> 7fa2acd84c69
Step 4/6 : COPY ./ /usr/src/app/
 ---> Using cache
 ---> e18584d3d0ab
Step 5/6 : EXPOSE 8888
 ---> Using cache
 ---> 86f4a2a8209b
Step 6/6 : CMD ["node","app.js"]
 ---> Using cache
 ---> f083ee0a541c
Successfully built f083ee0a541c
Successfully tagged nodeimg:latest
[Pipeline] sh
+ docker run -d -p 8810:8888 --name nodejsserver nodeimg
4f36a3f5f6c649aae65e0b1aa1cef7530b7af0bfedee934c53df1db01a40b8b3
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Deploy docker image through nginx reverse proxy)
[Pipeline] sh
+ cd ngnx/
+ docker build -t ngnximg .
Sending build context to Docker daemon  3.072kB

Step 1/2 : FROM nginx:alpine
alpine: Pulling from library/nginx
7264a8db6415: Pulling fs layer
3a753be56661: Pulling fs layer
f3063829fa47: Pulling fs layer
90fa5aa417ca: Pulling fs layer
bc66f7a3f388: Pulling fs layer
52972a58b96d: Pulling fs layer
1af8c8d8877f: Pulling fs layer
483e4ad92080: Pulling fs layer
bc66f7a3f388: Waiting
52972a58b96d: Waiting
1af8c8d8877f: Waiting
483e4ad92080: Waiting
90fa5aa417ca: Waiting
f3063829fa47: Verifying Checksum
f3063829fa47: Download complete
3a753be56661: Download complete
7264a8db6415: Download complete
7264a8db6415: Pull complete
3a753be56661: Pull complete
90fa5aa417ca: Verifying Checksum
90fa5aa417ca: Download complete
bc66f7a3f388: Verifying Checksum
bc66f7a3f388: Download complete
f3063829fa47: Pull complete
90fa5aa417ca: Pull complete
52972a58b96d: Verifying Checksum
52972a58b96d: Download complete
bc66f7a3f388: Pull complete
52972a58b96d: Pull complete
1af8c8d8877f: Download complete
1af8c8d8877f: Pull complete
483e4ad92080: Verifying Checksum
483e4ad92080: Download complete
483e4ad92080: Pull complete
Digest: sha256:cac882be2b7305e0c8d3e3cd0575a2fd58f5fde6dd5d6299605aa0f3e67ca385
Status: Downloaded newer image for nginx:alpine
 ---> eaf194063ee2
Step 2/2 : COPY ./default.conf etc/nginx/conf.d/
 ---> 008790bc6e03
Successfully built 008790bc6e03
Successfully tagged ngnximg:latest
[Pipeline] sh
+ docker run -d -p 80:80 --link nodejsserver --name nginxcont ngnximg
e11784a2798e39e74ff4265745715ac04ce4120e34b324b1240d0555bfc2940f
[Pipeline] }
[Pipeline] // stage
[Pipeline] }
[Pipeline] // node
[Pipeline] End of Pipeline
Finished: SUCCESS
