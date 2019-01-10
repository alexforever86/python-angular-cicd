# Python Flask CICD with Buddy.Works

**Buddy.Works** CI/CD of a **python flask** **Gunicorn** WSGI server using **Systemd** in **AWS EC2** on **Git** push.

### Variables

- APP_ENV

- AWS_ACCESS_KEY_ID

- AWS_SECRET_ACCESS_KEY

- DB_HOST

- DB_NAME

- PUBLIC_IP

- APP_PATH

- SECRET_KEY

- SSH_USERNAME

- WEB_URL

- APP_NAME



### Actions

##### 1. System Setup

Setup a SSH action to run the bash script in EC2 server.

```
sudo apt-get update
sudo apt-get install -y python3-pip \
	python3-dev \
	python3-tk \
	build-essential \
	libssl-dev \
	libffi-dev \
	python3-setuptools \
	vim \
  software-properties-common \
  libpq-dev \
  libxext6 \
  libxrender-dev \
  libsm6
    
sudo apt-get remove python-cryptography \
	python3-cryptography

# Making python3 default
sudo rm -rf /usr/bin/python
sudo ln -s /usr/bin/python3 /usr/bin/python

# Create App Directory
mkdir -p $APP_PATH

# Application log setup
mkdir -p /var/log/$APP_NAME
cd /var/log/$APP_NAME && sudo touch error.log access.log && sudo chmod 777 error.log access.log
```

##### 2. Upload files to server

Setup a SFTP action to upload all the application files to the $APP_PATH in server.

##### 3. Install python dependencies

Setup a SSH action to run the bash script in EC2 server.
```
pip3 install -r requirements.txt
```

##### 4. Environment setup

Setup a SSH action to run the bash script in EC2 server.

```
cat > .env <<- "EOF"
APP_ENV=$APP_ENV
SECRET_KEY=$SECRET_KEY
WEB_URL=$WEB_URL
DATABASE_URI=postgresql://$DB_USERNAME:$DB_PASSWORD@${DB_HOST}/${DB_NAME}
AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY
PATH=$APP_PATH
PYTHONUNBUFFERED=1
----------------
----------------
EOF
```

##### 5. Running the App server as a systemd service.

Setup a SSH action to run the bash script in EC2 server.

Note: The service file is in the repository.
```
sudo cp $$APP_NAME.service /etc/systemd/system/$APP_NAME.service
source .env
sudo systemctl restart $APP_NAME
sudo systemctl enable $APP_NAME
sudo systemctl status $APP_NAME
```
