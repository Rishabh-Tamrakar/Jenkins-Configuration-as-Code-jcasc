## Jenkins Configuration as code (jcasc)

Full explanation how to do work on JCASC Plugin [link](https://github.com/jenkinsci/configuration-as-code-plugin) 

<img src="https://github.com/Rishabh-Tamrakar/Jenkins-Configuration-as-Code-jcasc/blob/master/images/jcasc.JPG" align="right" height="100" width="100">

In this we are going to configure jenkins with code

Prerequisites:-\
1.jenkins installed in your system.\
2.configuratrion As Code plugin in jenkins.\
3.job DSl plugin in jenkins.

In jenkins configuration as code we create a yaml file which tells jenkins that this is the configuration it needs to take.

we will work on a case where we want to setup\
A. user and password\
B. access to user\
C. tools (git, jdk, maven)\
D. creating pipeline job\
E. giving script in pipeline to fatch code from github then build and deploy application on tomcat.

**Steps**\
You need to follow these steps to start working on Jenkins Configuration as Code

1.Install the plugin from Available plugins
![Alt text](https://github.com/Rishabh-Tamrakar/Jenkins-Configuration-as-Code-jcasc/blob/master/images/1.JPG)

2. After that You will find this plugin in Manage plugins.
![Alt text](https://github.com/Rishabh-Tamrakar/Jenkins-Configuration-as-Code-jcasc/blob/master/images/2.JPG)

3.seclect Jenkins Configuration as Code and these options will come, from here you can start working.
![Alt text](https://github.com/Rishabh-Tamrakar/Jenkins-Configuration-as-Code-jcasc/blob/master/images/3.JPG)

***you can keep yaml file anywhere on accessible source like in the machine where Jenkins in running or AWS S3 bucket***

let's start \
I’m naming my file as Jenkins.yaml

1. starting with simple message on jenkins.
```
jenkins:
  systemMessage: "jcasc demo\n\n"
```
2. now we need to set scm schekout and label
```
  numExecutors: 1
  mode: NORMAL
  scmCheckoutRetryCount: 3
  labelString: "master-label"
```
3. adding user and password for creating jenkins new user.
```
  securityRealm:
    local:
      allowsSignup: false
      users:
       - id: username
         password: pass
```       
4. giving authorization to user.
```
  authorizationStrategy:
    globalMatrix:
      grantedPermissions:
        - "Overall/Read:anonymous"
        - "Overall/Administer:authenticated"
```
5. solving security issue that will appear after adding securityRealm and authorizationStrategy.
```
  crumbIssuer: "standard"

  remotingSecurity:
    enabled: true
```   
6. now adding tools to jenkins
```
tool:
  git:
    installations:
    - home: "git"
      name: "Default"
  jdk:
    installations:
    - home: "/usr/lib/jvm/java-8-openjdk-amd64/"
      name: "myjava"
  maven:
    installations:
    - name: "mymvn"
      properties:
      - installSource:
          installers:
          - maven:
              id: "3.6.2"
 ```             
7. and finally creating pipeline job and giving script to fatch, build, deploy.
```
jobs:
  - script: >
      pipelineJob('Newjob') {
        definition {
          cps {
            script("""\
              pipeline {
                agent any
                tools {
                    maven 'mymvn'
                }
                stages {
                  stage('Clone source'){
                    steps {
                      git url: 'your repo address'
                    }
                  }
                  stage ('Build') {
                    steps {
                      sh 'mvn clean install test'
                    }
                  }
                  stage ('Deploy-To-Tomcat') {
                    steps {
                      sshagent(['key']) {
                        sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@tomcat_server_IP:addrerss_where_you_want_to_deploy'
                      }
                  }
                 }
                }
              }""".stripIndent())
          }
        }
      }

```
You may see script approval error

<img src="https://github.com/Rishabh-Tamrakar/Jenkins-Configuration-as-Code-jcasc/blob/master/images/script%20aprove%20error.JPG" align="center" height="200" width="500">

When we give file with script so this script need to be approved so go to manage plugins and use this option

<img src="https://github.com/Rishabh-Tamrakar/Jenkins-Configuration-as-Code-jcasc/blob/master/images/script%20apprve%20option.JPG" align="center" height="100" width="1000">

And approve the script

<img src="https://github.com/Rishabh-Tamrakar/Jenkins-Configuration-as-Code-jcasc/blob/master/images/script%20apprve.JPG" align="center" height="200" width="400">

**a sample jenkins.yaml file is present in this repo**

***DevSecOps in CASC is present on another branch DevSecOps-With-Jcasc*** [link](https://github.com/Rishabh-Tamrakar/Jenkins-Configuration-as-Code-jcasc/tree/DevSecOps-With-Jcasc)
