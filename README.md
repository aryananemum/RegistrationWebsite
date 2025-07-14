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
