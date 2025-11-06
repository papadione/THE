## Installation de Zabbix

    1. Mysql

        sudo apt update && sudo apt upgrade -y
        sudo apt install mysql-server -y
        sudo mysql_secure_installation
        sudo systemctl status mysql
        sudo systemctl start mysql
        sudo systemctl enable mysql
        sudo mysql
        ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'ton_mot_de_passe';
        FLUSH PRIVILEGES;
        EXIT;
        mysql -u root -p

    2. Zabbix
        $ sudo -s
        # wget https://repo.zabbix.com/zabbix/7.4/release/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest_7.4+ubuntu24.04_all.deb
        # dpkg -i zabbix-release_latest_7.4+ubuntu24.04_all.deb
        # apt update
        # apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent

        # mysql -uroot -p
            password
            mysql> create database zabbix character set utf8mb4 collate utf8mb4_bin;
            mysql> create user zabbix@localhost identified by 'password';
            mysql> grant all privileges on zabbix.* to zabbix@localhost;
            mysql> set global log_bin_trust_function_creators = 1;
            mysql> quit;
        # zcat /usr/share/zabbix/sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix

        # mysql -uroot -p
            password
            mysql> set global log_bin_trust_function_creators = 0;
            mysql> quit;

        Edit file /etc/zabbix/zabbix_server.conf
        DBPassword=password

        # systemctl restart zabbix-server zabbix-agent apache2
        # systemctl enable zabbix-server zabbix-agent apache2

## Installation de grafana 
    https://grafana.com/docs/grafana/latest/setup-grafana/installation/debian/


## Installation de ELK

    1. Installation des Dépendances 
        sudo apt update
        sudo apt install apt-transport-https
        sudo apt install openjdk-11-jdk -y
        java -version
        sudo nano /etc/environment
        JAVA_HOME="/usr/lib/jvm/java-11-openjdk-amd64"
        source /etc/environment
        echo $JAVA_HOME
        wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
        echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-9.x.list
        sudo apt-get update

    2. Installation de Elasticsearch

        sudo apt-get install elasticsearch
        sudo systemctl start elasticsearch
        sudo systemctl enable elasticsearch
        sudo systemctl status elasticsearch
        sudo nano /etc/elasticsearch/elasticsearch.yml
            network.host: 0.0.0.0
            discovery.seed_hosts: []
        sudo systemctl restart elasticsearch
        /usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic
        password: k53jGU-AwiqbWTJkyP56
        curl -X GET "localhost:9200"
    
    3. Installation de logstash

        sudo apt-get install logstash -y
        sudo systemctl start logstash
        sudo systemctl enable logstash
        sudo systemctl status logstash

    4. Installation de Kibana

        sudo apt-get install kibana
        sudo systemctl start kibana
        sudo systemctl enable kibana
        sudo systemctl status kibana
        sudo nano /etc/kibana/kibana.yml
            server.port: 5601
            server.host: "0.0.0.0"
            elasticsearch.hosts: ["http://localhost:9200"]
        sudo systemctl restart kibana


## Installation de NetBox
    sudo apt update && sudo apt upgrade -y
    
    1. Installation de la base de données PostgreSQL
        sudo apt install -y postgresql
        psql -V
        sudo -u postgres psql
            CREATE DATABASE netbox;
            CREATE USER netbox WITH PASSWORD 'ChoisirSonMotDePasse';
            ALTER DATABASE netbox OWNER TO netbox;
            \connect netbox;
            GRANT CREATE ON SCHEMA public TO netbox;
            \q
        psql --username netbox --password --host localhost netbox
            \conninfo
            \q

    2. Installation de Redis
        sudo apt install -y redis-server
        redis-server -v
        redis-cli ping
    
    3. Installation de NetBox
        sudo apt install -y python3 python3-pip python3-venv python3-dev \
        build-essential libxml2-dev libxslt1-dev libffi-dev libpq-dev \
        libssl-dev zlib1g-dev

        python3 -V
        sudo mkdir -p /opt/netbox/
        cd /opt/netbox/
        sudo apt install -y git
        sudo git clone https://github.com/netbox-community/netbox.git .

        sudo adduser --system --group netbox
        sudo chown --recursive netbox /opt/netbox/netbox/media/
        sudo chown --recursive netbox /opt/netbox/netbox/reports/
        sudo chown --recursive netbox /opt/netbox/netbox/scripts/

        cd /opt/netbox/netbox/netbox/
        sudo cp configuration_example.py configuration.py
        sudo nano configuration.py

        python3 ../generate_secret_key.py
        sudo nano configuration.py
        SECRET_KEY = 'clegeneree'

        sudo /opt/netbox/upgrade.sh

        sudo PYTHON=/usr/bin/python3.12 /opt/netbox/upgrade.sh

        source /opt/netbox/venv/bin/activate
        cd /opt/netbox/netbox
        python3 manage.py createsuperuser
        sudo ln -s /opt/netbox/contrib/netbox-housekeeping.sh /etc/cron.daily/netbox-housekeeping
        ls -l /etc/cron.daily/netbox-housekeeping
        source /opt/netbox/venv/bin/activate
        python3 manage.py runserver 0.0.0.0:8000 --insecure

    4. Gunicorn

        sudo cp /opt/netbox/contrib/gunicorn.py /opt/netbox/gunicorn.py
        sudo cp -v /opt/netbox/contrib/*.service /etc/systemd/system/
        sudo systemctl daemon-reload
        sudo systemctl enable --now netbox netbox-rq
        systemctl status netbox.service
    
    5. Installation du serveur HTTPS

        sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/netbox.key -out /etc/ssl/certs/netbox.crt
        sudo apt install -y apache2
        sudo cp /opt/netbox/contrib/apache.conf /etc/apache2/sites-available/netbox.conf
        sudo vi /etc/apache2/sites-available/netbox.conf
        sudo a2enmod ssl proxy proxy_http headers rewrite
        sudo a2ensite netbox
        sudo systemctl restart apache2







J5brHrAXFLQSif0K

python3 ../generate_secret_key.py
_VfiqIYbGZHE*eJ5xFi&UG=b8ZKJ%h9bKp(iO4crpeCuJLJLoZ

