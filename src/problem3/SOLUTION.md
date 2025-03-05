Provide your solution here:

Step 1: Initial Investigation
Start by gathering data to determine the cause of high memory usage.

1. Check memory usage:
    free -h
2. Identify top memory-consuming processes:
    ps aux --sort=-%mem | head -10

Scenario 1: If Memory Leak is Due to Another Service (Not NGINX)

1. Check all running services consuming high memory:
    ps aux --sort=-%mem | awk '{print $1, $2, $3, $11}' | head -10
2. Validate running services using systemd:
    systemctl list-units --type=service --state=running
3. Consult the DevOps team to confirm if the process is due to my team or hacker.
4. Kill unauthorized processes.
    kill -9 <PID>
5. Secure the VM

Scenario 2: If Memory Leak is in NGINX
1. Check memory usage per worker process.
    ps -eo pid,%mem,%cpu,cmd --sort=-%mem | grep nginx
2. Check worker process limits in the config.
    grep -E "worker_processes|worker_connections" /etc/nginx/nginx.conf
3. Inspect NGINX logs for anomalies
    tail -f /var/log/nginx/access.log
    tail -f /var/log/nginx/error.log
4. Check if keep-alive connections are too high
    netstat -an | grep :80 | wc -l
5. Analyze traffic sources.
    awk '{print $1}' /var/log/nginx/access.log | sort | uniq -c | sort -nr | head -10
6. Enable rate limiting in NGINX
    Add this to the config
    http {
        limit_req_zone $binary_remote_addr zone=one:10m rate=10r/s;
    }

