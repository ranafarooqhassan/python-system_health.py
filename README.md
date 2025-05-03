import psutil
import platform
import time
import json
import os
from datetime import datetime

# Load config
with open('config.json') as f:
    config = json.load(f)

# Create logs directory
os.makedirs('health_logs', exist_ok=True)

def get_system_info():
    """Collect system health metrics"""
    return {
        "os": f"{platform.system()} {platform.release()}",
        "cpu_usage": psutil.cpu_percent(interval=1),
        "cpu_cores": psutil.cpu_count(logical=False),
        "memory": psutil.virtual_memory().percent,
        "memory_gb": f"{psutil.virtual_memory().used / (1024**3):.1f}/{psutil.virtual_memory().total / (1024**3):.1f}",
        "disks": {disk.device: disk.percent for disk in psutil.disk_partitions() if disk.mountpoint in config["monitored_disks"]},
        "network": {
            "sent_mb": psutil.net_io_counters().bytes_sent / (1024**2),
            "recv_mb": psutil.net_io_counters().bytes_recv / (1024**2)
        },
        "timestamp": datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    }

def check_thresholds(data):
    """Compare metrics against config thresholds"""
    alerts = []
    if data["cpu_usage"] > config["thresholds"]["cpu"]:
        alerts.append(f"🚨 High CPU usage: {data['cpu_usage']}%")
    if data["memory"] > config["thresholds"]["memory"]:
        alerts.append(f"🚨 High Memory usage: {data['memory']}%")
    for disk, usage in data["disks"].items():
        if usage > config["thresholds"]["disk"]:
            alerts.append(f"🚨 High Disk usage ({disk}): {usage}%")
    return alerts

def log_to_file(data, alerts):
    """Save results to daily log file"""
    log_file = f"health_logs/system_health_{datetime.now().strftime('%Y-%m-%d')}.log"
    with open(log_file, 'a') as f:
        f.write(f"\n\n=== {data['timestamp']} ===\n")
        f.write(json.dumps(data, indent=2) + "\n")
        if alerts:
            f.write("ALERTS:\n" + "\n".join(alerts))

def display_health(data, alerts):
    """Console output with colors"""
    print(f"\n\033[1mSYSTEM HEALTH - {data['timestamp']}\033[0m")
    print(f"OS: {data['os']} | CPU: {data['cpu_usage']}% ({data['cpu_cores']} cores)")
    print(f"Memory: {data['memory']}% ({data['memory_gb']} GB)")
    print("Disks:")
    for disk, usage in data["disks"].items():
        print(f"  {disk}: {usage}%")
    print(f"Network: ▲{data['network']['sent_mb']:.1f}MB ▼{data['network']['recv_mb']:.1f}MB")
    if alerts:
        print("\n\033[91m" + "\n".join(alerts) + "\033[0m")

if __name__ == "__main__":
    try:
        while True:
            data = get_system_info()
            alerts = check_thresholds(data)
            display_health(data, alerts)
            log_to_file(data, alerts)
            time.sleep(config["check_interval_seconds"])
    except KeyboardInterrupt:
        print("\nMonitoring stopped.")
