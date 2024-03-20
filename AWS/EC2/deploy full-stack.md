
# Deploying Full Stack Apps to AWS EC2 with SQL Databases

### Create instance
[Follow here](https://github.com/dimahike/notes/blob/main/AWS/EC2/Creating%20an%20Instance%20in%20EC2%20(AWS).md)

### Setup EC2 Instance
Update and upgrade the system for the latest packages.
```bash
sudo apt update
sudo apt upgrade
```

### Install Node.js
you can install any version of node.js by replacing `20.x` with the version you want to install.
```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs
```
Or you can any app like `nvm` to manage multiple versions of node.js.

### rsync
Push to server from local machine (use `rsync` command in the app location)
```bash
rsync -avz --exclude 'node_modules' --exclude '.git' --exclude '.env' \
-e "ssh -i ~/.ssh/your-key.pem" \
. ubuntu@ip-address:~/app
```

### Database
Don't need it for mongoDB if you are using Atlas. But if you want to use it, here is how to install the two most popular databases.
#### MySQL
```bash
sudo apt install mysql-server

sudo systemctl start mysql
sudo systemctl enable mysql

sudo mysql -u root

CREATE DATABASE my_app;
CREATE USER 'my_app_user'@'localhost' IDENTIFIED WITH mysql_native_password BY 'MyNewPass1!';
GRANT ALL PRIVILEGES ON my_app.* TO 'my_app_user'@'localhost';
```

#### Postgres
```bash
sudo apt install postgresql postgresql-contrib

sudo systemctl start postgresql
sudo systemctl enable postgresql

sudo -i -u postgres

CREATE DATABASE my_app;
CREATE ROLE my_app_role WITH LOGIN PASSWORD 'some_password';
GRANT ALL PRIVILEGES ON DATABASE "my_app" TO my_app_role;
```

### systemd

#### Step 1: Create the Environment File
Create a new file for your environment variables and open the file in Vim:
```bash
sudo vim /etc/app.env
```
In Vim, add your variables in the format `VARIABLE=value`. For example:
```
DB_PASSWORD=your_secure_password
```
Note: to save and exit vim, press `esc` then `:wq` then `enter`

Restrict the file permissions for security.
```bash
sudo chmod 600 /etc/app.env
sudo chown ubuntu:ubuntu /etc/app.env
```

#### Step 2: Create the systemd Service File
Navigate to the systemd directory and create a new service file, `myapp.service`.
```bash
sudo vim /etc/systemd/system/myapp.service
```
Define the service settings. Add the following content in Vim, modifying as needed for your application:
```
[Unit]
Description=Node.js App
After=network.target multi-user.target

[Service]
User=ubuntu
WorkingDirectory=/home/ubuntu/app
ExecStart=/usr/bin/npm start
Restart=always
Environment=NODE_ENV=production
EnvironmentFile=/etc/app.env
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=myapp

[Install]
WantedBy=multi-user.target
```
Reload systemd and start your service.
```bash
sudo systemctl daemon-reload
sudo systemctl enable myapp.service
sudo systemctl start myapp.service
```
Verify that the service is running properly.
```bash
sudo systemctl status myapp.service
```

### View Logs
```bash
sudo journalctl -u myapp.service
```
tail logs:
```bash
sudo journalctl -fu myapp.service
```

#### Step 3: Build and Deploy the App
Install dependencies and build the app. You can do this on your local machine (use `rsync` command in the app location) or on the server.
```bash
npm install
npm run build
```

### Caddy

#### Step 1: Install Caddy
[https://caddyserver.com/docs/install](https://caddyserver.com/docs/install)

```bash
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https curl
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-stable.list
sudo apt update
sudo apt install caddy
sudo vim /etc/caddy/Caddyfile
:80 {
    reverse_proxy localhost:3000
}
sudo systemctl restart caddy
```

#### Step 2: Configure Caddy to Use HTTPS
Add a domain name for your server.

##### AWS Route 53 Domain Name
AWS Route 53 Domain Name

Setup a custom domain name in route 53 to start creating dns records for services within AWS. I'll show...

Update the Caddyfile to use your domain name and enable HTTPS.

Open the Caddyfile in Vim:
```bash
sudo vim /etc/caddy/Caddyfile
```
Add your domain name and the reverse proxy settings. For example:
```bash
mydomain.com {
    reverse_proxy localhost:3000
}
sudo systemctl restart caddy
```
<br/>

### References
**Easily Deploy Full Stack Node.js Apps on AWS EC2 | Step-by-Step Tutoriall]**
- [https://youtu.be/nQdyiK7-VlQ?si=4zmNNenN4mgKJQhL](https://youtu.be/nQdyiK7-VlQ?si=4zmNNenN4mgKJQhL)
- [https://www.sammeechward.com/deploying-full-stack-js-to-aws-ec2](https://www.sammeechward.com/deploying-full-stack-js-to-aws-ec2)
