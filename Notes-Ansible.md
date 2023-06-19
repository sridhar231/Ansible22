Installing Tomcat on Ubuntu:
---------------------------

Installing Java
sudo apt install openjdk-11-jdk
java -version
---------------------------------------------------------------------------------------
 creates a new system user and group with home directory /opt/tomcat that will run the Tomcat service:
 sudo useradd -m -U -d /opt/tomcat -s /bin/false tomcat
 -----------------------------------------------------------------------------------
 Download the Tomcat zip file to the /tmp directory using the wget command:

wget https://www-eu.apache.org/dist/tomcat/tomcat-10/v${VERSION}/bin/apache-tomcat-${VERSION}.tar.gz -P /tmp
------------------------------------------------
Once the Tomcat tar file is downloaded, extract it to the /opt/tomcat directory:
sudo tar -xf /tmp/apache-tomcat-${VERSION}.tar.gz -C /opt/tomcat/
-----------------------------------------------------------------------------------------
 create a symbolic link named latest
 sudo ln -s /opt/tomcat/apache-tomcat-${VERSION} /opt/tomcat/latest
 
 -------------------------------------------------------------------------------------
 
 Change the directory ownership 
 sudo chown -R tomcat: /opt/tomcat
 --------------------------------------------------------------------------------------------
 The shell scripts inside the Tomcat’s bin directory must be executable in order to run:
 sudo sh -c 'chmod +x /opt/tomcat/latest/bin/*.sh'
 
 --------------------------------------------------------------------------------------------------
 Creating SystemD Unit File #
 
 Tomcat will run as a service.

Open your text editor and create a tomcat.service unit file in the /etc/systemd/system/ directory:

sudo nano /etc/systemd/system/tomcat.service

Paste the following configuration:

[Unit]
Description=Tomcat 10 servlet container
After=network.target

[Service]
Type=forking

User=tomcat
Group=tomcat

Environment="JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64"
Environment="JAVA_OPTS=-Djava.security.egd=file:///dev/urandom -Djava.awt.headless=true"

Environment="CATALINA_BASE=/opt/tomcat/latest"
Environment="CATALINA_HOME=/opt/tomcat/latest"
Environment="CATALINA_PID=/opt/tomcat/latest/temp/tomcat.pid"
Environment="CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC"

ExecStart=/opt/tomcat/latest/bin/startup.sh
ExecStop=/opt/tomcat/latest/bin/shutdown.sh

[Install]
WantedBy=multi-user.target
-----------------------------------------------------------------------------------------
Save and close the file and run the following command to notify systemd that we created a new unit file:
sudo systemctl daemon-reload
-----------------------------------------------------------------------------------------
Enable and start the Tomcat service:
sudo systemctl enable --now tomcat
-----------------------------------------------------------------------------------------
Check the service status:

sudo systemctl status tomcat

The output should show that the Tomcat server is enabled and running:
● tomcat.service - Tomcat 10 servlet container
     Loaded: loaded (/etc/systemd/system/tomcat.service; enabled; vendor preset: enabled)
     Active: active (running) since Sat 2022-12-24 18:53:37 UTC; 6s ago
    Process: 5124 ExecStart=/opt/tomcat/latest/bin/startup.sh (code=exited, status=0/SUCCESS)
   Main PID: 5131 (java)
...
----------------------------------------------------------------------------------------------
You can start, stop, and restart Tomcat the same as any other systemd service:

sudo systemctl start tomcat
sudo systemctl stop tomcat
sudo systemctl restart tomcat


===================================================================
1. sudo apt update
2. sudo apt install openjdk-11-jdk -y
3. sudo useradd -m -U -d /opt/tomcat -s /bin/false tomcat
4. wget https://www-eu.apache.org/dist/tomcat/tomcat-10/v${VERSION}/bin/apache-tomcat-${VERSION}.tar.gz -P /tmp
5. sudo tar -xf /tmp/apache-tomcat-${VERSION}.tar.gz -C /opt/tomcat/
6. sudo ln -s /opt/tomcat/apache-tomcat-${VERSION} /opt/tomcat/latest
7. sudo chown -R tomcat: /opt/tomcat
8. sudo sh -c 'chmod +x /opt/tomcat/latest/bin/*.sh'
9. sudo nano /etc/systemd/system/tomcat.service
10. sudo systemctl daemon-reload
11. sudo systemctl enable --now tomcat
12. sudo systemctl status tomcat
13. sudo systemctl start tomcat
    sudo systemctl stop tomcat
    sudo systemctl restart tomcat
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
1. sudo apt update
2. sudo apt install openjdk-11-jdk -y
   - name: install java
      ansible.builtin.apt:
        name: "{{ java_package }}"
        update_cache: yes
        state: present
3. sudo useradd -m -U -d /opt/tomcat -s /bin/false tomcat
   - name: create group
      ansible.builtin.group:
        name: "{{ group }}"
        state: present
   - name: create user
      ansible.builtin.user:
        name: "{{ user }}"
        create_home: yes
        home: "{{ homedir }}"
        shell: "{{ shell }}"
        group: "{{ group }}"     
4. wget https://www-eu.apache.org/dist/tomcat/tomcat-10/v${VERSION}/bin/apache-tomcat-${VERSION}.tar.gz -P /tmp

   - name: download tomcat
      ansible.builtin.get_url:
        url: "https://www-eu.apache.org/dist/tomcat/tomcat-{{ tomcat_major_version }}/v{{ tomcat_version }}/bin/apache-tomcat-{{ tomcat_version }}.tar.gz"
        dest: "/tmp/apache-tomcat-{{ tomcat_version }}.tar.gz"
&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&----destination dought
5. sudo tar -xf /tmp/apache-tomcat-${VERSION}.tar.gz -C /opt/tomcat/
   - name: find if the tomcat is already extracted
      ansible.builtin.stat:
        path: "{{ homedir }}/apache-tomcat-{{ tomcat_version }}/LICENSE"
      register: license_info
    - name: extract tomcat
      ansible.builtin.unarchive:
        src: "/tmp/apache-tomcat-{{ tomcat_version }}.tar.gz"
        dest: "{{ homedir }}"
        remote_src: yes
      when: not license_info.stat.exists
6. sudo ln -s /opt/tomcat/apache-tomcat-${VERSION} /opt/tomcat/latest
   - name: create a symlink
      ansible.builtin.file:
        src: "{{ homedir }}/apache-tomcat-{{ tomcat_version }}"
        dest: "{{ homedir }}/latest"
        state: link
      notify:
        - Recursively change ownership of a directory
        - Add execute permissions for shell scripts
    - name: Flush handlers
      meta: flush_handlers
7. sudo chown -R tomcat: /opt/tomcat
   ** - name: Recursively change ownership of a directory
      ansible.builtin.file:
        path: "{{ homedir }}"
        state: directory
        recurse: yes
        owner: "{{ user }}"
        group: "{{ group }}"
   
8. sudo sh -c 'chmod +x /opt/tomcat/latest/bin/*.sh'
      - name: Add execute permissions for shell scripts
      ansible.builtin.shell:
        cmd: "chmod +x {{ homedir }}/latest/bin/*.sh"

    * Steps 7 & 8 are omited or replaced by in step-5 (find if the tomcat is already extracte)    
   
9.  sudo nano /etc/systemd/system/tomcat.service
    - name: copy the service file
      ansible.builtin.template:
        src: tomcat.service.j2
        dest: /etc/systemd/system/tomcat.service
      notify:
        - reload daemon
10. sudo systemctl daemon-reload
11. sudo systemctl enable --now tomcat
12. sudo systemctl status tomcat
13. sudo systemctl start tomcat
    sudo systemctl stop tomcat
    sudo systemctl restart tomcat    

     name: ensure tomcat is enable and runing
      ansible.builtin.systemd:
        name: tomcat
        enabled: yes
        state: started

