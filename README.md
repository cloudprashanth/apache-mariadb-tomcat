### Documentation to setup web your application.

#### Assuming the following architecture , Doc has been made.

![image](https://user-images.githubusercontent.com/29029753/50553231-9daf1380-0cc8-11e9-9281-1ff2f3685d04.png)


### 1. Install and Start Web Server.

```
$ sudo yum install httpd -y
$ sudo systemctl enable httpd
$ sudo systemctl start httpd
```

##### Configure Web Server.
```
$ sudo cat /etc/httpd/conf.d/studentapp.conf
ProxyPass "/student"  "http://localhost:8080/student"
ProxyPassReverse "/student"  "http://localhost:8080/student"

$ sudo wget https://raw.githubusercontent.com/citb34/project-1-documentation/master/httpd-index.html -O /var/www/html/index.html

$ sudo systemctl restart httpd
```


### 2. Install and Start Tomcat Server.

While setting up any application it is always a good practice to create a user and run the application using that user.
For that we are going to use `student` user to run out student application.

#### Add user and install java
```
$ sudo useradd student
$ sudo yum install java -y
```

#### Download and setup App Server and run it as student user
```
$ sudo su - student 

student $ wget -qO- http://mirrors.wuchna.com/apachemirror/tomcat/tomcat-8/v8.5.37/bin/apache-tomcat-8.5.37.tar.gz | tar -xz
student $ cd apache-tomcat-8.5.37
student $ wget https://github.com/citb34/project-1-documentation/raw/master/studentapp.war -O webapps/student.war
student $ vim conf/context.xml
<Resource name="jdbc/TestDB" auth="Container" type="javax.sql.DataSource"
               maxTotal="100" maxIdle="30" maxWaitMillis="10000"
               username="USERNAME" password="PASSWORD" driverClassName="com.mysql.jdbc.Driver"
               url="jdbc:mysql://RDS-ENDPOINT:3306/DATABASE"/>

##### Add the above content just before the last line, Last line usually looks like </Context>
##### Note: Replace USERNAME, PASSWORD, RDS-ENDPOINT, DATABASE with associated RDS values after creating it.
```

Switch back to CentOS user and run the following commands.
```
$ sudo wget https://raw.githubusercontent.com/citb34/project-1-documentation/master/tomcat-init -O /etc/init.d/tomcat
$ sudo chmod ugo+x /etc/init.d/tomcat
$ sudo systemctl daemon-reload
$ sudo systemctl start tomcat
$ sudo systemctl enable tomcat
```



# To setup application with WEB and API.

![image](https://user-images.githubusercontent.com/29029753/50938864-2fc0d400-14a0-11e9-9ffa-6b2f9ea7e2b4.png)



### 1. Install and Start Web Server.

```
$ sudo yum install httpd -y
$ sudo systemctl enable httpd
$ sudo systemctl start httpd
```

##### Configure Web Server.
```
$ sudo cat /etc/httpd/conf.d/studentapp.conf
ProxyPass "/student"  "http://localhost:8080/student"
ProxyPassReverse "/student"  "http://localhost:8080/student"

ProxyPass "/api" "http://localhost:8090/student"
ProxyPassReverse "/api"  "http://localhost:8090/student"

$ sudo wget https://raw.githubusercontent.com/citb34/project-1-documentation/master/httpd-index.html -O /var/www/html/index.html

$ sudo systemctl restart httpd
```

### 2. Install and Start Tomcat Server.

While setting up any application it is always a good practice to create a user and run the application using that user.
For that we are going to use `student` user to run out student application.

#### Add user and install java
```
$ sudo useradd student
$ sudo yum install java -y
```

#### Download and setup Tomcat Server and run it as student user
```
$ sudo su - student 

student $ wget -qO- http://mirrors.wuchna.com/apachemirror/tomcat/tomcat-8/v8.5.37/bin/apache-tomcat-8.5.37.tar.gz | tar -xz
student $ cd apache-tomcat-8.5.37
student $ wget https://github.com/citb34/project-1-documentation/raw/master/studentapp.war -O webapps/student.war
student $ vim conf/context.xml
<Resource name="jdbc/TestDB" auth="Container" type="javax.sql.DataSource"
               maxTotal="100" maxIdle="30" maxWaitMillis="10000"
               username="USERNAME" password="PASSWORD" driverClassName="com.mysql.jdbc.Driver"
               url="jdbc:mysql://RDS-ENDPOINT:3306/DATABASE"/>

##### Add the above content just before the last line, Last line usually looks like </Context>
##### Note: Replace USERNAME, PASSWORD, RDS-ENDPOINT, DATABASE with associated RDS values after creating it.
```
Switch back to CentOS user and run the following commands.
```
$ sudo wget https://raw.githubusercontent.com/citb34/project-1-documentation/master/tomcat-init -O /etc/init.d/tomcat
$ sudo chmod ugo+x /etc/init.d/tomcat
$ sudo systemctl daemon-reload
$ sudo systemctl start tomcat
$ sudo systemctl enable tomcat
```

### 3. Setup API Application.

Run the commands as student user
```
$ mkdir /home/student/api
$ cd /home/student/api
$ wget https://s3-us-west-2.amazonaws.com/studentapi-cit/studentapi.war -O studentapi.war
$ sed -i -e "/RDS/ d" -e "$ a export RDS_ENDPOINT=rds.com\nexport RDS_USER=student\nexport RDS_PASS=student1" /home/student/.bashrc
```

### Note: RDS values need to be changed as per RDS instance.

Run the following commands as root user

```
# wget https://raw.githubusercontent.com/citb34/project-1-documentation/master/studentapi-init -O /etc/init.d/studentapi
# chmod +x /etc/init.d/studentapi
# systemctl daemon-reload
# systemctl restart studentapi
```
