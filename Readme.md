#!/bin/bash

echo "====== SERVER PERFORMANCE STATS ======"
echo

# OS Version
echo "OS Version:"
cat /etc/os-release | grep PRETTY_NAME | cut -d= -f2 | tr -d '"'
echo

# Uptime
echo "Uptime:"
uptime -p
echo

# Load Average
echo "Load Average (1m, 5m, 15m):"
uptime | awk -F'load average:' '{ print $2 }'
echo

# Logged in Users
echo "Currently Logged In Users:"
who | wc -l
echo

# Failed login attempts (last 24 hours)
echo "Failed Login Attempts (last 24h):"
journalctl _COMM=sshd --since "24 hours ago" | grep "Failed password" | wc -l
echo

# CPU Usage
echo "Total CPU Usage:"
top -bn1 | grep "Cpu(s)" | awk '{print "Used: " 100 - $8 "%, Idle: " $8 "%"}'
echo

# Memory Usage
echo "Total Memory Usage:"
free -m | awk 'NR==2{
  used=$3;
  free=$4;
  total=$2;
  printf "Used: %dMB, Free: %dMB, Usage: %.2f%%\n", used, free, (used/total)*100
}'
echo

# Disk Usage
echo "Total Disk Usage (/):"
df -h / | awk 'NR==2 {
  printf "Used: %s, Free: %s, Usage: %s\n", $3, $4, $5
}'
echo

# Top 5 processes by CPU
echo "Top 5 Processes by CPU Usage:"
ps -eo pid,comm,%cpu --sort=-%cpu | head -n 6
echo

# Top 5 processes by Memory
echo "Top 5 Processes by Memory Usage:"
ps -eo pid,comm,%mem --sort=-%mem | head -n 6
echo

echo "====== END OF REPORT ======"

# Project URL
https://roadmap.sh/projects/server-stats
https://github.com/Johnterrie/server_stats