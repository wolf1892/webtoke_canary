# Detecting phishing detection[MITM] using webtoken
ref:https://zolder.io/using-honeytokens-to-detect-aitm-phishing-attacks-on-your-microsoft-365-tenant/

nginx
```
load_module /usr/lib/nginx/modules/ngx_mail_module.so;
load_module /usr/lib/nginx/modules/ngx_http_headers_more_filter_module.so;
load_module /usr/lib/nginx/modules/ngx_stream_module.so;

user www-data;
error_log /var/log/nginx/error.log;

http {
    log_format main '{ "timestamp": "$time_iso8601", '
                        '"remote_addr": "$remote_addr", '
                        '"remote_user": "$remote_user", '
                        '"body_bytes_sent": "$body_bytes_sent", '
                        '"request_time": "$request_time", '
                        '"status": "$status", '
                        '"request": "$request", '
                        '"request_method": "$request_method", '
                        '"http_referrer": "$http_referer", '
                        '"http_x_forwarded_for": "$http_x_forwarded_for", 
                        '"http_user_agent": "$http_user_agent" }';

    server {
        listen 80;

        access_log /var/log/nginx/access.log main;

        location / {
            default_type text/html;
            root   /usr/share/nginx/html/;
            include mime.types;
            index  index.html;
        }
    }
}

```
[NGINX configuration]
This configuration ensures that access logs are formatted correctly for analysis. Additionally, it sets up a server listening on port 80 to handle incoming requests.

Injecting Script for Detection
To detect MITM attacks, we'll inject a hidden script into the server's CSS file. This script will log information about incoming requests, allowing us to identify any anomalies.

Just like suggested by reference blog

[Injected CSS script]
By embedding this script within the CSS file, we create a covert monitoring mechanism. Any changes to the http_referrer field in the logs indicate potential malicious activity.

Real-Time Monitoring
With our defenses in place, let's monitor incoming traffic for signs of a MITM attack. Every time a user accesses our website, the NGINX server logs relevant information, including the http_referrer field.


[Logged information example]
By analyzing these logs in real-time, we can detect any deviations from expected behavior. If the http_referrer field points to anything other than the actual server, it could signal a MITM attack in progress.
