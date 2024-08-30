
To automate the process of running tmux with openvpn as root and ensure that it runs persistently, you can create a script and configure it to run automatically on system startup. Here’s a step-by-step guide:

1. Create a Script
Create a script to start tmux and run openvpn. For example, create a file named start_openvpn.sh:
    
    sudo nano /usr/local/bin/start_openvpn.sh

Add the following content to the script:

bash
Copy code
#!/bin/bash

# Start tmux session and run OpenVPN
tmux new-session -d -s openvpn 'openvpn /home/saibot/profile-windows.ovpn'
-d starts the tmux session in detached mode.
-s openvpn names the tmux session openvpn.
Save and close the editor (in Nano, press Ctrl+X, then Y, and Enter).

2. Make the Script Executable
Make sure the script is executable:

bash
Copy code
sudo chmod +x /usr/local/bin/start_openvpn.sh
3. Set Up Systemd Service
To run the script automatically on startup, create a systemd service file.

Create a file named openvpn.service in /etc/systemd/system/:

bash
Copy code
sudo nano /etc/systemd/system/openvpn.service
Add the following content:

ini
Copy code
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
4. Enable and Start the Service
Reload the systemd manager configuration to recognize the new service, enable it to start on boot, and start it immediately:

bash
Copy code
sudo systemctl daemon-reload
sudo systemctl enable openvpn
sudo systemctl start openvpn
5. Verify the Service
To check the status of the service:

bash
Copy code
sudo systemctl status openvpn
This command will show if the service is running and if there are any errors.

Summary
Create a script to start tmux and run openvpn.
Make the script executable.
Create a systemd service to run the script on startup.
Enable and start the service.
This setup ensures that openvpn will start automatically in a tmux session every time your system boots.
