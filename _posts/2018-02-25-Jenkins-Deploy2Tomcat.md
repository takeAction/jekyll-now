---
layout : post
title : Jenkins Deploy2Tomcat
categories : [Project Management]
---

1. Install JDK

2. Install Jenkins, you can add `restart` after your jenkins url to restart jenkins.  
   
   It consist of following default folder:
   
   ```
   /usr/lib/jenkins
   /var/lib/jenkins
   /etc/sysconfig/jenkins
   /var/log/jenkins/jenkins.log
   ```

3. Install Maven and SVN

4. Install Tomcat, make sure tomcat has **host-manager** and **manager** app, otherwise war cannot be deployed.

   Also edit `tomcat-users.xml`, add `<user username="deployer" password="deployer" roles="manager-script" />`, this
   user name/password is used to connect to tomcat for Jenkins, this user role should contain `manager-script`.

5. Create a Jenkins project, enter a project name then choose **Freestyle project**, if project name is `HelloWorld`, then
   Jenkins will create a `HelloWorld` folder under `/var/lib/jenkins/workspace/`
   
   ![_config.yml]({{ site.baseurl }}/images/jenkins-1.png)
   
6. In **General** tab, you can check `Discard old builds`, strategy is `Log rotation`, then input `max # of builds to keep`,
   this is optional.
   
   ![_config.yml]({{ site.baseurl }}/images/jenkins-2.png)
   
7. Config **Source Code Management**, you can check `Git` or `Subversion`.
   
   For `Subversion`, input `Repository URL` and `Credentials` which is user name and password to check out source code from 
   svn. 
   Then choose the `repository depth`.
   
   If `Repository URL` is `http://192.168.2.102:8090/svn/test/JenkinTest`, then Jenkins will create a folder named 
   `JenkinTest` under `/var/lib/jenkins/workspace/<your project name>/`.
   If it detects svn has changes, then it will check out source code from svn to this folder and build it.
   
   ![_config.yml]({{ site.baseurl }}/images/jenkins-3.png)
   
8. Config **Build Triggers**, that is when to trigger the build automatically.
   
   If you check `Poll SCM` and `Schedule` is `H/5 * * * *`, it means Jenkins will check svn every 5 minutes, if code has
   change, then it will run the build process.

   ![_config.yml]({{ site.baseurl }}/images/jenkins-4.png)
   
9. **Add Build Step**, if you use Maven to build, you can select `Invoke top-level Maven targets`, then input maven goals
   to build.
   
   ![_config.yml]({{ site.baseurl }}/images/jenkins-5.png)
   
10. **Add post build action** to deploy war to tomcat after install **Deploy to container** plugin. 

    `deploy war/ear to a container` should be selected.
    
    value of `war/ear files` should be `<your svn project folder name>/target/*.war` or `*/target/*.war`
    if this value is wrong, then Jenkins cannot run this depoly action, there is no error message in console output 
    and `/var/log/jenkins/jenkins.log`.
    
    In this section, `Context Path` is your web app name in tomcat, you can leave it empty.
    
    Then select container and input the user name/password to connect it.
    
    Input `Tomcat URL`, like `http://192.168.2.102:8181/`
    
    ![_config.yml]({{ site.baseurl }}/images/jenkins-6.png)
    
### Problems
    
   Jenkins installation successfully base on above steps in my pc. However it failed in another linux pc.
   
   Jenkins is **2.108** in my pc, **2.107.1** in failed pc. I have no idea that why they are different because I installed it
   base on same instruction of jenkins doc.
   
   both they are centos 7 and have same jdk version 1.8.
   
   Maven is installed local in them.
      
#### Cannot run program "mvn" (in directory ...):error=2, No such file or directory
   
  Specify maven installation in Jenkins:
  Jenkins -> Global Tool Configuration -> Maven, input maven name and maven home, uncheck `Install automatically`.
  Then in `Build` section of project config, select maven version instead default.
     
  ![_config.yml]({{ site.baseurl }}/images/jenkins-maven.PNG)
     
  ![_config.yml]({{ site.baseurl }}/images/jenkins-build.PNG)
   
#### JAVA_HOME is not defined correctly
   
  Go to Manage Jenkins -> Configure System -> Global Properties -> Check `Environment variables` -> input `JAVA_HOME` and JAVA_HOME
  PATH.
     
   ![_config.yml]({{ site.baseurl }}/images/jenkins-jdk.PNG)
   
#### Cannot delete `*.pom` in jenkins workspace
   
  ![_config.yml]({{ site.baseurl }}/images/jenkins-build-env.PNG)
  
  or change permission manually.
  
#### Failed to undeploy

  The Tomcat Manager responded "FAIL - Context [/Test] is defined in server.xml and may not be undeployed.
  
  As the error message implies, there is context Test defined in server.xml which cause this problem.
  
  Go to tomcat -> conf -> server.xml, find out like:
  
  ```XML
  <Host name="localhost"  appBase="/var/www/webapps" unpackWARs="true" autoDeploy="true">
      <Context docBase="/var/www/webapps/Test" path="/Test" reloadable="false" />  
  ```
  delete context definition.
  
  **Note: If you change webapp directory, like above, then the manager and host-manager should be moved, in this case, they should
  be in /var/www/webapps/**
  
