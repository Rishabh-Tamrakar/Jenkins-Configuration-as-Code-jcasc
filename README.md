## A use case where we will use Jcasc and build and deploy an application in secure way this approch is known as DevSecOps
In this approch we will use two open source tools

1. [gitleaks](https://github.com/zricethezav/gitleaks)\
gitleaks is a tool that will find secrets in your repository like keys, passwords you can also configure this tool to works according to you.

2. [OWASP ZAP](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project)\
Zap is to tell you about security vulnerabilities in your web applications while you are developing and testing your applications.

Prerequisites:-\
1.Docker installed in your system where jenkins is running, we will use zap through container.\
2.gitleaks should also be configured there only.

**Now we need to add gitleaks and give address of repository and Zap after build and deployment of application.**


Adding a stage in script for gitleaks that will create a report also
```
  stages {
    stage('script') {
      steps {
        sh'''
        gitleaks -v --repo=https://your-repo.git --report=gitleaks_results.csv || true
        '''
       }
   }
```

Another script that will pull docker imageof zap and scan your application for vulnerabilities
```
    stage('script1') {
      steps{
        sh'''
        docker pull owasp/zap2docker-stable
        rm -rf "$(pwd)/Report"
        mkdir -p "$(pwd)/Report"
        chmod 777 "$(pwd)/Report"
        chown jenkins:jenkins "$(pwd)/Report"
        '''
       }
     }
    stage('scrpit2'){
      steps{
        sh'''
        docker run -v $(pwd)/Report:/zap/wrk/:rw -t owasp/zap2docker-stable zap-baseline.py -t http://adress-of-your-application/ -g Baseline.conf -r BaseLine-Scan-Report.html || true
      '''
      }
    }
```

**Note - You need yo add jenkins user in sudoers to run these scripts**\
sudoers file is present in /etc/sudoers\
add this line\
jenkins ALL=(ALL) NOPASSWD: ALL\
and save file\
**Now give jenkins user permission to read and write for docker**\
sudo setfacl -m user:jenkins:rw /var/run/docker.sock
after that approve script and build

A comple [jcasc DevSecOps.yaml](https://github.com/Rishabh-Tamrakar/Jenkins-Configuration-as-Code-jcasc/blob/DevSecOps-With-Jcasc/jcasc%20DevSecOps.yaml) file is present in repository
