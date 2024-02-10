###Continuous Delivery with Jenkins and Ansible

- In this demo Jenkins Pipeline automates the end-to-end software delivery process, 
encompassing Continuous Integration (CI) and Continuous Deployment (CD) stages. 
Leveraging Maven for building, SonarQube for code analysis, and Nexus Repository 
for artifact management, it ensures high-quality code by executing unit and 
integration tests while enforcing code standards through Checkstyle. The same 
pipeline will install Tomcat on an AWS EC2 instance, downloads artifacts from a 
Nexus repository, and deploys them onto the Tomcat EC2 server.

![image](https://github.com/prashant842/Continuous-Delivery-with-Jenkins-and-Ansible/assets/110713882/6225f434-544f-4430-95e1-7a5f8ebbdb55)




###
### Prerequisites
- JDK 1.8 or later
- Maven 3 or later
- MySQL 5.6 or later

### Technologies 
- Spring MVC
- Spring Security
- Spring Data JPA
- Maven
- JSP
- MySQL
### Database
Here,we used Mysql DB 
MSQL DB Installation Steps for Linux ubuntu 14.04:
- $ sudo apt-get update
- $ sudo apt-get install mysql-server

Then look for the file :
- /src/main/resources/accountsdb
- accountsdb.sql file is a mysql dump file.we have to import this dump to mysql db server
- > mysql -u <user_name> -p accounts < accountsdb.sql


