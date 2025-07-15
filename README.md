# RegistrationWebsite

### Instructions to deploy on AWS EC2

ssh into AWS instance

sudo apt update
sudo apt install python3-pip python3-venv nginx -y

Clone this repository into the instance

cd <this_repository>
python3 -m venv venv
source venv/bin/activate
pip install flask gunicorn

Ensure the main py file has "app = Flask(__name__)"

gunicorn -w 3 -b 127.0.0.1:8000 registration:app

Double check that it is working

curl http://127.0.0.1:8000

### Configure Nginx as reverse proxy

sudo nano /etc/nginx/sites-available/flaskapp

Copy and paste the following into this file:

server {
    listen 80;
    server_name 3.142.143.116;  # change this to either domain name or public IP address of AWS instance

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}

Then we can enable the site

sudo ln -s /etc/nginx/sites-available/flaskapp /etc/nginx/sites-enabled
sudo nginx -t
sudo systemctl restart nginx

Set up gunicorn

sudo nano /etc/systemd/system/flaskapp.service

Paste the below code:

[Unit]
Description=Gunicorn instance to serve Flask app
After=network.target

[Service]
User=ubuntu
Group=www-data
WorkingDirectory=/home/ubuntu/RegistrationWebsite
Environment="PATH=/home/ubuntu/RegistrationWebsite/venv/bin"
ExecStart=/home/ubuntu/RegistrationWebsite/venv/bin/gunicorn -w 3 -b 127.0.0.1:8000 registration:app

[Install]
WantedBy=multi-user.target

Then you can enable it

sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl start flaskapp
sudo systemctl enable flaskapp

You can then connect to the site using: http://3.142.143.116/

TLS
don't use port 80
caddy
