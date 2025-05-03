# System Health Monitor

A Python script to monitor key system health metrics including CPU, memory, disk, and network usage.

## Features
- Real-time monitoring of system resources
- Lightweight and easy to configure
- Supports Windows, Linux, and macOS
- Clear console output formatting

## Requirements
- Python 3.6+
- psutil library (`pip install psutil`)

## Installation
1. Clone this repository:
   ```bash
   git clone https://github.com/yourusername/system-health-monitor.git
Navigate to the project directory:

bash
cd system-health-monitor
Install dependencies:

bash
pip install -r requirements.txt
Usage
Run the script directly:

bash
python system_health.py
Sample output:

============== System Health Check ==============
System: Windows | Node Name: DESKTOP-ABC123 | Release: 10
CPU Usage: 15.5% | Cores: 8
Memory Usage: 45.2% (7.2 GB used / 16.0 GB total)
Disk Usage (C:): 60.1% (120 GB used / 200 GB total)
Network: Sent: 45.2 MB | Received: 102.4 MB
=================================================
Customization
Edit the script to:

Add additional metrics

Change refresh intervals

Implement logging to file

Set warning thresholds

Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.
