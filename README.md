## A use case where we will use Jcasc and build and deploy an application in secure way this approch is known as DevSecOps
In this approch we will use two open source tools

1. [gitleaks](https://github.com/zricethezav/gitleaks)\
gitleaks is a tool that will find secrets in your repository like keys, passwords you can also configure this tool to works according to you.

2. [OWASP ZAP](owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project)\
Zap is to tell you about security vulnerabilities in your web applications while you are developing and testing your applications.

Prerequisites:-\
1.Docker installed in your system where jenkins is running we will use zap through container.\
2.gitleaks should also be configured there only.

**Now we need to add steps after build and deployment of application.**
