
# Install OpenJDK 11
```
yum install java-11-openjdk-devel -y
```

# Check Java version
java --version

# Create a user for Tomcat
useradd -m -U -d /opt/tomcat -s /bin/false tomcat

# Download Apache Tomcat 10.1.7
# Visit the specified URL to get the latest version if needed
wget https://dlcdn.apache.org/tomcat/tomcat-10/v10.1.7/bin/apache-tomcat-10.1.7.tar.gz

# Extract the downloaded file
gunzip <FileName>
tar -xvpf <FileName>

# Move the extracted directory to tomcat10
mv <Dir name> tomcat10

# Set ownership of the Tomcat directory to the tomcat user
chown -R tomcat:tomcat /opt/tomcat10

# Create a systemd service file for Tomcat
vim /etc/systemd/system/tomcat.service

# Add the following content to the file
# [Unit] and [Service] sections define the service properties
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

# Reload systemd configurations
systemctl daemon-reload

# Start Tomcat
systemctl start tomcat

# Enable Tomcat to start on system boot
systemctl enable tomcat

# Check the status of the Tomcat service
systemctl status tomcat

# Configure Tomcat users
vim /opt/tomcat10/conf/tomcat-users.xml

# Add the following roles and user to allow access to Tomcat Manager
<role rolename="manager-gui"/>
<role rolename="manager-script"/>
<role rolename="manager-jmx"/>
<role rolename="manager-status"/>
<user username="tomcat" password="tomcat" roles="manager-gui, manager-script, manager-jmx, manager-status"/>

# Configure remote access for Tomcat Manager
vim /opt/tomcat10/webapps/manager/META-INF/context.xml

# Add the following Valve configuration to allow remote access
<Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />

# Repeat the same configuration for host-manager
vim /opt/tomcat10/webapps/host-manager/META-INF/context.xml
<Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />

# Restart Tomcat to apply the changes
systemctl restart tomcat

# Check the status of the Tomcat service after restart
systemctl status tomcat




Import the Jenkins repository GPG key.
```
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
```
