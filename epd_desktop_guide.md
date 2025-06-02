# Complete E-Paper Desktop Setup Guide

A comprehensive guide to setting up a fully automated e-paper desktop computer using Raspberry Pi 5, IT8951 controller, and PaperTTY.

## Hardware Requirements

- Raspberry Pi 5
- E-paper display with IT8951 controller
- MicroSD card (32GB+ recommended)
- Power supply for Pi 5
- Network connection (WiFi or Ethernet)

## Software Overview

This setup creates an automated e-paper desktop that:
- Starts VNC server automatically on boot
- Connects PaperTTY to display the desktop on e-paper
- Provides full LXDE desktop environment
- Allows remote VNC access from other devices
- Launches applications properly without authentication issues

## Step 1: Initial Raspberry Pi Setup

1. **Install Raspberry Pi OS**
   - Flash Raspberry Pi OS (64-bit) to SD card
   - Enable SSH during setup
   - Configure WiFi/network access

2. **Initial System Update**
   ```bash
   sudo apt update
   sudo apt upgrade -y
   ```

3. **Enable SPI Interface**
   ```bash
   sudo raspi-config
   ```
   - Navigate to Interface Options → SPI → Enable

## Step 2: Install and Configure PaperTTY

1. **Install Dependencies**
   ```bash
   sudo apt install python3-pip python3-venv git -y
   ```

2. **Clone PaperTTY Repository**
   ```bash
   git clone https://github.com/joukos/PaperTTY.git
   cd PaperTTY
   ```

3. **Set Up Python Virtual Environment**
   ```bash
   python3 -m venv papertty_venv
   source papertty_venv/bin/activate
   pip install --upgrade pip
   pip install -e .
   ```

4. **Test PaperTTY Installation**
   ```bash
   ./papertty_venv/bin/papertty --driver IT8951 terminal
   ```
   - You should see terminal output on your e-paper display

## Step 3: Install and Configure VNC Server

1. **Install TightVNC Server**
   ```bash
   sudo apt install tightvncserver -y
   ```

2. **Set VNC Password**
   ```bash
   vncpasswd
   ```
   - Enter a secure password for VNC access
   - This will be used for remote connections

3. **Test Manual VNC Server Start**
   ```bash
   tightvncserver :1 -geometry 936x702 -depth 24
   ```
   - Note: 936x702 is half the resolution of 1872x1404 for better readability

## Step 4: Configure Desktop Environment

1. **Create VNC Startup Script**
   ```bash
   nano ~/.vnc/xstartup
   ```

   Add the following content:
   ```bash
   #!/bin/sh
   export XKL_XMODMAP_DISABLE=1
   export GDK_BACKEND=x11
   unset SESSION_MANAGER
   unset DBUS_SESSION_BUS_ADDRESS
   exec startlxde-pi
   ```

2. **Make the script executable**
   ```bash
   chmod +x ~/.vnc/xstartup
   ```

## Step 5: Test VNC and PaperTTY Integration

1. **Start VNC Server**
   ```bash
   tightvncserver :1 -geometry 936x702 -depth 24
   ```

2. **Test PaperTTY VNC Connection**
   ```bash
   cd ~/PaperTTY
   ./papertty_venv/bin/papertty --driver IT8951 vnc --display 1 --password YOUR_VNC_PASSWORD
   ```

3. **Test Desktop Components** (in a separate SSH session)
   ```bash
   export DISPLAY=:1
   export GDK_BACKEND=x11
   lxpanel &
   pcmanfm --desktop &
   ```

## Step 6: Fix Application Launch Issues

The key issue is X11 authorization. We need to disable access control for the VNC server.

1. **Kill current VNC session**
   ```bash
   tightvncserver -kill :1
   ```

2. **Start VNC with disabled access control**
   ```bash
   tightvncserver :1 -geometry 936x702 -depth 24 -ac
   ```
   - The `-ac` flag disables X access control

## Step 7: Create Automated Services

### 7.1 Create VNC Service

1. **Create systemd service file**
   ```bash
   sudo nano /etc/systemd/system/vnc-epd.service
   ```

2. **Add service configuration**
   ```ini
   [Unit]
   Description=TightVNC Server for EPD
   After=network.target

   [Service]
   Type=forking
   User=username
   Environment=HOME=/home/username
   Environment=USER=username
   WorkingDirectory=/home/username
   ExecStart=/usr/bin/tightvncserver :1 -geometry 936x702 -depth 24 -ac
   ExecStop=/usr/bin/tightvncserver -kill :1
   Restart=always
   RestartSec=5

   [Install]
   WantedBy=multi-user.target
   ```
   
   **Note:** Replace `username` with your actual username.

### 7.2 Create PaperTTY Service

1. **Create password file**
   ```bash
   echo "YOUR_VNC_PASSWORD" > /home/username/.vnc_password
   chmod 600 /home/username/.vnc_password
   ```

2. **Create PaperTTY startup script**
   ```bash
   nano /home/username/start_papertty.sh
   ```

   Add this content:
   ```bash
   #!/bin/bash
   cd /home/username/PaperTTY
   PASSWORD=$(cat /home/username/.vnc_password)
   exec ./papertty_venv/bin/papertty --driver IT8951 vnc --display 1 --password "$PASSWORD"
   ```

3. **Make script executable**
   ```bash
   chmod +x /home/username/start_papertty.sh
   ```

4. **Create PaperTTY systemd service**
   ```bash
   sudo nano /etc/systemd/system/papertty-epd.service
   ```

   Add this configuration:
   ```ini
   [Unit]
   Description=PaperTTY EPD Display
   After=vnc-epd.service
   Requires=vnc-epd.service

   [Service]
   Type=simple
   User=username
   Environment=HOME=/home/username
   WorkingDirectory=/home/username
   ExecStartPre=/bin/sleep 15
   ExecStart=/home/username/start_papertty.sh
   Restart=always
   RestartSec=30

   [Install]
   WantedBy=multi-user.target
   ```

### 7.3 Enable and Start Services

1. **Enable services to start on boot**
   ```bash
   sudo systemctl enable vnc-epd.service
   sudo systemctl enable papertty-epd.service
   ```

2. **Start services**
   ```bash
   sudo systemctl start vnc-epd.service
   sudo systemctl start papertty-epd.service
   ```

3. **Check service status**
   ```bash
   sudo systemctl status vnc-epd.service
   sudo systemctl status papertty-epd.service
   ```

## Step 8: Remove Annoying Error Popups

1. **Disable lxpolkit to remove authentication popup**
   ```bash
   sudo mv /usr/bin/lxpolkit /usr/bin/lxpolkit.disabled
   ```

2. **Restart VNC service**
   ```bash
   sudo systemctl restart vnc-epd.service
   ```

## Step 9: Add SSH Status Display (Optional)

1. **Edit bashrc for automatic status display**
   ```bash
   nano ~/.bashrc
   ```

2. **Add at the end of the file**
   ```bash
   # Auto-display EPD services status on SSH login
   if [ -n "$SSH_CONNECTION" ]; then
       echo "========================================="
       echo "  E-Paper Desktop Services Status"
       echo "========================================="
       echo ""
       echo "VNC Server Status:"
       sudo systemctl status vnc-epd.service --no-pager -l
       echo ""
       echo "PaperTTY Status:"
       sudo systemctl status papertty-epd.service --no-pager -l
       echo ""
       echo "========================================="
       echo ""
   fi
   ```

3. **Configure sudo permissions for status commands**
   ```bash
   sudo visudo
   ```

   Add this line at the end:
   ```bash
   username ALL=(ALL) NOPASSWD: /bin/systemctl status vnc-epd.service, /bin/systemctl status papertty-epd.service
   ```

4. **Add status alias**
   ```bash
   echo 'alias epd-status="sudo systemctl status vnc-epd.service papertty-epd.service --no-pager"' >> ~/.bashrc
   ```

## Step 10: Final Testing

1. **Reboot the system**
   ```bash
   sudo reboot
   ```

2. **After reboot, verify everything is working:**
   - SSH into the Pi - you should see service status automatically
   - Check that your e-paper display shows the desktop
   - Test applications by launching them from the desktop
   - Test remote VNC connection from another device

## Troubleshooting

### Common Issues and Solutions

**Issue: Applications won't launch from desktop menus**
- Solution: Ensure the VNC server is started with `-ac` flag to disable access control

**Issue: "GdkWaylandDisplay to GdkX11Display" error**
- Solution: Make sure `export GDK_BACKEND=x11` is in the xstartup file

**Issue: PaperTTY can't connect to VNC**
- Solution: Check VNC password file permissions and ensure services start in correct order

**Issue: Authentication popup appears**
- Solution: Disable lxpolkit by renaming the binary

**Service Status Commands:**
```bash
# Check individual services
sudo systemctl status vnc-epd.service
sudo systemctl status papertty-epd.service

# View service logs
sudo journalctl -u vnc-epd.service -f
sudo journalctl -u papertty-epd.service -f

# Restart services
sudo systemctl restart vnc-epd.service
sudo systemctl restart papertty-epd.service
```

## Remote Access

Once everything is set up, you can connect to your e-paper desktop remotely:

**From Windows:**
- Use RealVNC Viewer or similar VNC client
- Connect to: `PI_IP_ADDRESS:5901` (e.g., `192.168.1.100:5901`)
- Use the VNC password you set earlier

**Resolution Notes:**
- E-paper display: 936x702 (optimized for readability)
- Original display resolution: 1872x1404
- The 936x702 makes text and UI elements twice as large for better visibility

## Final Result

You now have a fully automated e-paper desktop computer that:
- ✅ Starts automatically on boot
- ✅ Displays a functional LXDE desktop on e-paper
- ✅ Launches applications without authentication issues
- ✅ Provides remote VNC access
- ✅ Shows service status on SSH login
- ✅ Survives reboots reliably

Enjoy your unique e-paper desktop setup! 🖥️📄✨
