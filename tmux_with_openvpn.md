
To automate the process of running tmux with openvpn as root and ensure that it runs persistently, you can create a script and configure it to run automatically on system startup. Here’s a step-by-step guide:

# 1. Create a Script
Create a script to start tmux and run openvpn. For example, create a file named start_openvpn.sh:
    
    sudo nano /usr/local/bin/start_openvpn.sh

Add the following content to the script:

    #!/bin/bash
    
    # Start tmux session and run OpenVPN
    tmux new-session -d -s openvpn 'openvpn /home/saibot/profile-windows.ovpn'
-d starts the tmux session in detached mode.
-s openvpn names the tmux session openvpn.
Save and close the editor (in Nano, press Ctrl+X, then Y, and Enter).

# 2. Make the Script Executable
Make sure the script is executable:


    sudo chmod +x /usr/local/bin/start_openvpn.sh
# 3. Set Up Systemd Service
To run the script automatically on startup, create a systemd service file.

Create a file named openvpn.service in /etc/systemd/system/:
        
    sudo nano /etc/systemd/system/openvpn.service

Add the following content:


    [Unit]
    Description=OpenVPN in tmux
    After=network.target
    
    [Service]
    Type=forking
    ExecStart=/usr/local/bin/start_openvpn.sh
    User=root
    Restart=always
    
    [Install]
    WantedBy=multi-user.target

# OPTIONAL
To configure OpenVPN to use credentials from a file instead of prompting you every time, you need to modify your OpenVPN configuration file (.ovpn) and create a separate file for your credentials. Here’s a step-by-step guide to accomplish this:

a). Create a Credentials File
Create a file for your credentials (e.g., auth.txt).

Add your username and password to this file, each on a separate line:

    username
    password

Make sure to replace username and password with your actual VPN credentials.
Save the file and ensure it is stored securely.

b). Modify the OpenVPN Configuration File
Open your OpenVPN configuration file (.ovpn) in a text editor.

Locate the line that starts with auth-user-pass. This line tells OpenVPN that it should prompt for a username and password.

Modify the line to include the path to your credentials file. For example:

    auth-user-pass /path/to/auth.txt

Replace /path/to/auth.txt with the actual path to the credentials file you created.
Save the configuration file.

c). Set File Permissions
Set the correct file permissions to ensure that the credentials file is not readable by unauthorized users:

    chmod 600 /path/to/auth.txt

This command ensures that only the file owner can read and write the file.

# 4. Enable and Start the Service
Reload the systemd manager configuration to recognize the new service, enable it to start on boot, and start it immediately:
        
    sudo systemctl enable openvpn
    sudo systemctl start openvpn
    sudo systemctl daemon-reload
    
# 5. Verify the Service
To check the status of the service:

    sudo systemctl status openvpn
This command will show if the service is running and if there are any errors.

# Summary
Create a script to start tmux and run openvpn.
Make the script executable.
Create a systemd service to run the script on startup.
Enable and start the service.
This setup ensures that openvpn will start automatically in a tmux session every time your system boots.
