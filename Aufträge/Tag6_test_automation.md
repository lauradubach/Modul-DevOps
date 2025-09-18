# Instanz erstellen

1. EC2 Instanz
2. Wichtig Instance type: t3.large (sonst zu wenig speicher)
3. Port 9000 öffnen

4. Cloud Config in der Instanz hinterlegen:

```bash
#cloud-config
packages:
  - podman
  - podman-compose
  - python3-pip
write_files:
  - path: /etc/containers/registries.conf.d/registries.conf
    permissions: '0644'
    owner: root:root
    content: |
      unqualified-search-registries = ["docker.io"]
```

5. Via SSH verbinden
6. Ordner erstellen zb: sonarqube
7. Docker herunterladen: `sudo apt  install podman-compose`
8. docker-compose.yml im neuen Ordner erstellen mit folgendem Inhalt:

```bash
services:
  sonarqube:
    image: sonarqube:community
    hostname: sonarqube
    container_name: sonarqube
    read_only: true
    depends_on:
      db:
        condition: service_healthy
    environment:
      SONAR_JDBC_URL: jdbc:postgresql://db:5432/sonar
      SONAR_JDBC_USERNAME: sonar
      SONAR_JDBC_PASSWORD: sonar
    volumes:
      - sonarqube_data:/opt/sonarqube/data
      - sonarqube_extensions:/opt/sonarqube/extensions
      - sonarqube_logs:/opt/sonarqube/logs
      - sonarqube_temp:/opt/sonarqube/temp
    ports:
      - "9000:9000"
    networks:
      - ${NETWORK_TYPE:-ipv4}
  db:
    image: postgres:17
    healthcheck:
      test: [ "CMD-SHELL", "pg_isready -d $${POSTGRES_DB} -U $${POSTGRES_USER}" ]
      interval: 10s
      timeout: 5s
      retries: 5
    hostname: postgresql
    container_name: postgresql
    environment:
      POSTGRES_USER: sonar
      POSTGRES_PASSWORD: sonar
      POSTGRES_DB: sonar
    volumes:
      - postgresql:/var/lib/postgresql
      - postgresql_data:/var/lib/postgresql/data
    networks:
      - ${NETWORK_TYPE:-ipv4}

volumes:
  sonarqube_data:
  sonarqube_temp:
  sonarqube_extensions:
  sonarqube_logs:
  postgresql:
  postgresql_data:

networks:
  ipv4:
    driver: bridge
    enable_ipv6: false
  dual:
    driver: bridge
    enable_ipv6: true
    ipam:
      config:
        - subnet: "192.168.2.0/24"
          gateway: "192.168.2.1"
        - subnet: "2001:db8:2::/64"
          gateway: "2001:db8:2::1"
```
9. Nun im Ordner `podman-compose up -d` ausführen
10. Dann `podman-compose up` 
11. Nun im Browser auf die Website zugreiffen: http://<AWS-VM-IP>:9000