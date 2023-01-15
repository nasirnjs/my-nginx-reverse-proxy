# How To Configure Nginx as a Reverse Proxy
Step1: Installing Nginx
sudo apt-get update
sudo apt-get install nginx

Step2: Configuring your Server Block
sudo vim /etc/nginx/sites-available/yourdomain.com.bd

server {

  	server_name yourdomain.com.bd;

	error_log /var/log/nginx/yourdomain.com.bd.error.log;
	access_log /var/log/nginx/yourdomain.com.bd.access.log;
	
	location /
        {
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Host $host;
                proxy_set_header X-Forwarded-Server $host;
                proxy_set_header X-Forwarded-Port $server_port;
                proxy_set_header X-Forwarded-Proto $scheme;
		proxy_pass http://localhost:8060;
        }

    listen 443 ssl;
    ssl_certificate /etc/letsencrypt/live/yourdomain.com.bd/fullchain.pem; 
    ssl_certificate_key /etc/letsencrypt/live/yourdomain.com.bd/privkey.pem; 
    include /etc/letsencrypt/options-ssl-nginx.conf; 
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;
}
server {
    if ($host = yourdomain.com.bd) {
        return 301 https://$host$request_uri;
    }

  	server_name yourdomain.com.bd;
    listen 80;
    return 404; 

}

Step3: Enable Nginx configuration
`sudo ln -s /etc/nginx/sites-available/yourdomain /etc/nginx/sites-enabled/`

You can now test your configuration file for syntax errors
`sudo nginx -t`

With no problems reported, restart Nginx to apply your changes
`sudo systemctl restart nginx`

`sudo systemctl enable nginx`



