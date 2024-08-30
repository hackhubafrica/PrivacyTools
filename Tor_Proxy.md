# Routing All Traffic Through a SOCKS Proxy
This guide will help you route all traffic from your local machine through a SOCKS proxy. Instructions are provided for both Windows and Unix-based systems (Linux and macOS).

# Windows
1. Set Up a SOCKS Proxy
Using PuTTY:

Download and install PuTTY.
Open PuTTY and configure an SSH connection to a remote server.

In the "Connection" category, expand "SSH" and select "Tunnels".

    Add a new forwarded port:
    
    Source Port: 1080 (or any other preferred local port)

Destination: Dynamic

Click "Add" and then "Open" to establish the SSH connection.

Using Tor:

Download and install the Tor Browser.

Start Tor, which will run a SOCKS proxy on localhost:9150.

2. Configure Windows to Use the SOCKS Proxy

Open Control Panel → "Network and Internet" → "Internet Options".

Go to the "Connections" tab and click on "LAN settings".

Check "Use a proxy server for your LAN".

Click "Advanced" and set:

    Socks: 127.0.0.1
    
    Port: 1080 (or 9150 for Tor)

3. Route All Traffic Using Proxifier

Download and install Proxifier.

Add a new proxy server:

Address: 127.0.0.1

Port: 1080 (or 9150 for Tor)
Protocol: SOCKS5
Go to Profile → Proxification Rules and add a rule to direct all traffic through the SOCKS proxy.

4. Verify Configuration
Visit check.torproject.org to verify you're using Tor.
Test with curl:

       curl --socks5-hostname 127.0.0.1:9050 http://check.torproject.org

Visit whatismyipaddress.com to confirm your IP is masked.

# Linux and macOS
1. Set Up a SOCKS Proxy
Using SSH:
Open a terminal and run:

        ssh -D 1080 -N -f user@remote_server

Replace user with your username and remote_server with the server's address.
2. Configure System to Use the SOCKS Proxy
On macOS, use the networksetup command to configure the proxy.
On Linux, use redsocks to route all TCP connections through the SOCKS proxy.
3. Configure redsocks (Linux)
Install redsocks:

    sudo apt-get install redsocks

Edit /etc/redsocks.conf:

    base {
        log_debug = on;
        log_info = on;
        log = "file:/var/log/redsocks.log";
        daemon = on;
        redirector = iptables;
    }
    
    redsocks {
        local_ip = 127.0.0.1;
        local_port = 12345;
        ip = 127.0.0.1;
        port = 9050;
        type = socks5;
    }

Restart the redsocks service:

    sudo systemctl restart redsocks
    sudo systemctl enable redsocks

4. Redirect Traffic Through redsocks
Add iptables rules:

       sudo iptables -t nat -A OUTPUT -p tcp --dport 80 -j REDIRECT --to-ports 12345
       sudo iptables -t nat -A OUTPUT -p tcp --dport 443 -j REDIRECT --to-ports 12345

Save iptables rules:

    sudo sh -c "iptables-save > /etc/iptables/rules.v4"
5. Verify Configuration
Ensure Tor is running:

       sudo systemctl status tor
Ensure redsocks is running:

    sudo systemctl status redsocks

Test the setup by visiting check.torproject.org to confirm traffic is routed through Tor.
Additional Notes
local_ip and local_port in redsocks.conf define where redsocks listens for incoming traffic (usually 127.0.0.1:12345).
ip and port specify the SOCKS proxy (e.g., Tor on 127.0.0.1:9050).
By following these steps, you can successfully route all your machine's traffic through a SOCKS proxy, ensuring enhanced privacy and security.

