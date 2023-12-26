## Ensure an internet connection is available.

```
ping google.com -c 5
```

## Ensure to update the System


Update the Systme
```
sudo yum upgrade -y
```

Reboot the system
```
reboot
```

## Follow these steps to download and install tomcat 

Install OpenJDK 11
```
yum install java-11-openjdk-devel -y
```

Check Java version
```
java --version
```



Create a user for Tomcat
```
useradd -m -U -d /opt/tomcat -s /bin/false tomcat
```



Download Apache Tomcat
Visit the specified URL to get the latest version if needed
```
https://tomcat.apache.org/download-10.cgi
```
Download Apache Tomcat 10.1.7
```
wget https://dlcdn.apache.org/tomcat/tomcat-10/v10.1.7/bin/apache-tomcat-10.1.7.tar.gz
```



Extract the downloaded file
```
gunzip <FileName>
tar -xvpf <FileName>
```


Rename the extracted directory to tomcat10
```
mv <Dir name> tomcat10
```


Set ownership of the Tomcat directory to the tomcat user
```
chown -R tomcat:tomcat /opt/tomcat10
```


Create a systemd service file for Tomcat
```
vim /etc/systemd/system/tomcat.service
```

Add the following content to the file
```
[Unit]
Description=Apache Tomcat 10 Servlet container
Wants=network.target
After=network.target

[Service]
Type=forking
User=tomcat
Group=tomcat
Environment="JAVA_HOME=/usr/lib/jvm/jre"
Environment="JAVA_OPTS=-Djava.security.egd=file:///dev/urandom"
Environment="CATALINA_BASE=/opt/tomcat10"
Environment="CATALINA_HOME=/opt/tomcat10"
Environment="CATALINA_PID=/opt/tomcat10/temp/tomcat.pid"
Environment="CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC"
ExecStart=/opt/tomcat10/bin/startup.sh
ExecStop=/opt/tomcat10/bin/shutdown.sh
Restart=always

[Install]
WantedBy=multi-user.target
```

Reload systemd configurations
```
systemctl daemon-reload
```

Start Tomcat
```
systemctl start tomcat
```

Enable Tomcat to start on system boot
```
systemctl enable tomcat
```


Check the status of the Tomcat service
```
systemctl status tomcat
```





Remove some line in the Configure file in Tomcat Manager
```
vim /opt/tomcat10/webapps/manager/META-INF/context.xml
```

Remove the following Valve configuration
```
<Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />
```

Remove some line in the Configure file in Tomcat host-manager
```
vim /opt/tomcat10/webapps/host-manager/META-INF/context.xml
```
Remove it
```
<Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />
```


Configure Tomcat users
```
vim /opt/tomcat10/conf/tomcat-users.xml
```

Add the following roles and user to allow access to Tomcat Manager
```
<role rolename="manager-gui"/>
<role rolename="manager-script"/>
<role rolename="manager-jmx"/>
<role rolename="manager-status"/>
<user username="tomcat" password="tomcat" roles="manager-gui, manager-script, manager-jmx, manager-status"/>
```


Restart Tomcat to apply the changes
```
systemctl restart tomcat
```



Check the status of the Tomcat service after restart
```
systemctl status tomcat
```

Open Google chrome and run your IP with ports 8080
```
http://<IP Address>:8080
```
