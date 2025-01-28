CREATE USER 'exporter'@'%' IDENTIFIED BY 'P0rt02025!';
GRANT PROCESS, REPLICATION CLIENT, SELECT ON *.* TO 'exporter'@'%';
FLUSH PRIVILEGES;


wget https://github.com/prometheus/mysqld_exporter/releases/download/v0.16.0/mysqld_exporter-0.16.0.linux-amd64.tar.gz

tar -xvzf mysqld_exporter-0.16.0.linux-amd64.tar.gz

sudo mv mysqld_exporter-0.16.0.linux-amd64/mysqld_exporter /usr/local/bin/mysqld_exporter

sudo chmod +x /usr/local/bin/mysqld_exporter

sudo nano /etc/.my.cnf

[client]
user=exporter
password=P0rt02025!
host=185.185.50.230
port=3306

sudo chmod 600 /etc/.my.cnf

sudo nano /etc/systemd/system/mysqld_exporter.service

#------
[Unit]
Description=Prometheus MySQL Exporter
After=network.target

[Service]
User=root
Group=root
ExecStart=/usr/local/bin/mysqld_exporter --config.my-cnf=/etc/.my.cnf
Restart=always

[Install]
WantedBy=multi-user.target

#------
sudo systemctl enable mysqld_exporter
sudo systemctl start mysqld_exporter


openssl req -x509 -newkey rsa:4096 -keyout server-key.pem -out server-cert.pem -days 365 -nodes -subj "/CN=mysql-exporter"



/usr/local/bin/mysqld_exporter \
  --config.my-cnf=/etc/.my.cnf \
  --web.tls-cert-file=/root/prometheus/server-cert.pem \
  --web.tls-key-file=/root/prometheus/server-key.pem
