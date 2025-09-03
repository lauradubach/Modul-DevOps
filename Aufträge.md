# Manuelle Installationsanleitung für Ecommerce (Port 80)

## Voraussetzungen

- Zugriff auf ein AWS Learner-Lab.
- Download [Flask-Python-E-Commerce-Website.zip](https://gitlab.com/ch-tbz-wb/Stud/devops/-/blob/main/2_Unterrichtsressourcen/3_Projekt/01_Flask-Python-E-Commerce-Website.zip)

## Netzwerk und Infrastruktur manuell einrichten (AWS Beispiel)

Falls nicht mit Terraform automatisiert, manuell:

- Erstelle eine VPC mit CIDR `10.11.5.0/24`.
- Erstelle zwei Subnets in Availability Zone `us-east-1a`:
 - DMZ (public subnet): `10.11.5.192/24`
 - Default (private subnet): `10.11.5.0/24`
- Erstelle Internet Gateway und verbinde mit VPC.
- Erstelle NAT Gateway in DMZ Subnet mit Elastic IP.
- Route Tables:
 - Public Route Table: Route 0.0.0.0/0 über IGW, assoziiert mit DMZ Subnet.
 - Private Route Table: Route 0.0.0.0/0 über NAT Gateway, assoziiert mit Default Subnet.
- Erstelle Security Groups:
 - `webshop_sg`: Erlaube TCP 22, 80 von überall, alle ausgehend.
 - `webserveronly`: Erlaube TCP 22, 3306 nur aus internen Subnetzen, alle ausgehend.
- Erstelle Network ACLs, die alle eingehenden und ausgehenden Verbindungen erlauben.
- Erstelle Key Pair `webserver` mit eigenem SSH Public Key.

## Webserver manuell einrichten

### Server vorbereiten

- Ubuntu Server in DMZ Subnet starten. (EC2 Instanz erstellen)
- IPs:
 - Private IP: `10.11.5.200`
 - Public IP: Elastic IP (zuweisen)

### Pakete installieren

```shell
sudo apt update && sudo apt upgrade -y

sudo apt install -y nginx git python3-pip python3-venv sqlite3 mariadb-client-core pkg-config libmariadb-dev libmariadb-dev-compat python-is-python3
```

### Entpacke Zipfile

```shell
sudo unzip -d /opt/Flask-Python-E-Commerce-Website Flask-Python-E-Commerce-Website.zip
```

### Virtual Environment und Abhängigkeiten installieren

```shell
sudo python3 -m venv /opt/Flask-Python-E-Commerce-Website/.venv
source /opt/Flask-Python-E-Commerce-Website/.venv/bin/activate
pip install --upgrade pip
pip install -r /opt/Flask-Python-E-Commerce-Website/requirements.txt
pip install cs50
deactivate
```

### Systemd Service anlegen

Erstelle die Datei `/etc/systemd/system/ecommerce.service` mit folgendem Inhalt:

```md
[Unit]
Description=Ecommerce Gunicorn Service
After=network.target

[Service]
WorkingDirectory=/opt/Flask-Python-E-Commerce-Website
Environment="PATH=/opt/Flask-Python-E-Commerce-Website/.venv/bin"
ExecStart=/opt/Flask-Python-E-Commerce-Website/.venv/bin/gunicorn --bind 0.0.0.0:8080 application:app --access-logfile /var/log/gunicorn/ecommerce.access.log --error-logfile /var/log/gunicorn/ecommerce.error.log
Restart=always

[Install]
WantedBy=multi-user.target
```

### Nginx konfigurieren

Erstelle die Datei `/etc/nginx/sites-available/ecommerce` mit folgendem Inhalt:

```md
server {
    listen 80;
    location / {
        proxy_pass http://localhost:8080/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        auth_basic "Restricted Access";
        auth_basic_user_file /etc/nginx/.htpasswd;
    }
}
```

Erstelle die Datei `/etc/nginx/.htpasswd` mit folgendem Inhalt (Passwort für Benutzer `webuser`):

```md
webuser:$apr1$3haQNqEx$w949g6QxSudppxigTf4HW/
```

Aktiviere die Nginx-Site:

```shell
sudo ln -s /etc/nginx/sites-available/ecommerce /etc/nginx/sites-enabled/ecommerce
sudo rm -f /etc/nginx/sites-enabled/default
sudo nginx -t
sudo systemctl restart nginx
```

### Dienst starten und aktivieren

```shell
sudo mkdir /var/log/gunicorn
sudo systemctl daemon-reload
sudo systemctl enable ecommerce.service
sudo systemctl start ecommerce.service
```

### Flask-Service Benutzer anpassen

Im Log wird beim ersten Mal ersichtlich, dass noch ein falscher User hinterlegt ist. 
`/opt/Flask-Python-E-Commerce-Website/application.py`

```md
# Creates a connection to the database
db = SQL ( "mysql://shopuser:password@10.11.5.5:3306/ecommerce" )
```

_shopuser & password anpassen (2 entfernen)_ 

---

## 4 Datenbankserver manuell einrichten

### Server vorbereiten

- Ubuntu Server im privaten Subnet (10.11.5.0/26) starten
- Private IP: `10.11.5.5`
- Kein Public IP (nicht öffentlich erreichbar)

### MariaDB installieren und konfigurieren

```shell
sudo apt update && sudo apt upgrade -y
sudo apt install -y mariadb-server
```

### MariaDB konfigurieren

- MariaDB so konfigurieren, dass sie auf allen Interfaces lauscht:

```shell
sudo sed -i 's/^bind-address\s*=.*$/bind-address = 0.0.0.0/' /etc/mysql/mariadb.conf.d/50-server.cnf || \
sudo sed -i 's/^bind-address\s*=.*$/bind-address = 0.0.0.0/' /etc/mysql/my.cnf
sudo systemctl restart mariadb
```

### MariaDB absichern und Datenbanken anlegen

```shell
sudo mysql -e "DELETE FROM mysql.user WHERE User='';"
sudo mysql -e "DROP DATABASE IF EXISTS test;"
sudo mysql -e "FLUSH PRIVILEGES;"
sudo mysql -e "CREATE DATABASE IF NOT EXISTS ecommerce;"
sudo mysql -e "CREATE USER IF NOT EXISTS 'shopuser'@'%' IDENTIFIED BY 'password';"
sudo mysql -e "GRANT ALL PRIVILEGES ON ecommerce.* TO 'shopuser'@'%';"
sudo mysql -e "FLUSH PRIVILEGES;"
```

### Datenbank importieren

- Übertrage die Datei `ecommerce.sql` auf den Datenbankserver (z.B. via SCP).
- Importiere die Daten:

```shell
sudo mysql -t ecommerce -e "source /pfad/zu/ecommerce.sql"
```

---

## 5 DNS-Einträge

- Erstelle in Route53 eine private Zone `webshop.tbz` und verknüpfe sie mit der VPC.
- Füge private DNS-Einträge hinzu:
 - `webserver.webshop.tbz` → private IP des Webservers (10.11.5.200)
 - `dbserver.webshop.tbz` → private IP des DB-Servers (10.11.5.5)

---

## 6 Zusammenfassung

- Webserver läuft mit Nginx als Reverse Proxy auf Port 80 für Ecommerce.
- Ecommerce Webapplikation läuft in Gunicorn-Systemd-Service in einer virtuellen Umgebung.
- Datenbank läuft auf separatem Server mit MariaDB, sicher konfiguriert und mit einer Datenbank für Ecommerce.
- Private DNS sorgt für Namensauflösung innerhalb der VPC.
- Sicherheitsgruppen und NACLs sind so konfiguriert, dass nur notwendiger Traffic erlaubt ist.
 

auf den server kopieren:
`scp -i /c/Users/laura/.ssh/webserver.pem /c/Users/laura/Downloads/01_Flask-Python-E-Commerce-Website.zip ubuntu@34.199.217.66:/home/ubuntu`

entzippen:

`sudo unzip -o 01_Flask-Python-E-Commerce-Website.zip -d /opt/`

