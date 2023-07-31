# Домашнее задание к занятию "`9.2. Zabbix. Часть 1`" - `Наталья Мартынова (Пономарева)`

---

### Задание 1

1. Установка репозитория Zabbix
```
wget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.0-4%2Bubuntu22.04_all.deb
sudo dpkg -i zabbix-release_6.0-4+ubuntu22.04_all.deb
sudo apt update
```
2. Установка Zabbix Server с фронтом
```
sudo apt install zabbix-server-pgsql zabbix-frontend-php php8.1-pgsql zabbix-nginx-conf zabbix-sql-scripts zabbix-agent
```
3. Установка PostgreSQL
```
sudo apt install postgresql
```

4. Создание роли и БД в PostgreSQ
```
su - postgres -c 'psql --command "CREATE USER zabbix WITH PASSWORD '\'123456789\'';"'
su - postgres -c 'psql --command "CREATE DATABASE zabbix OWNER zabbix;"'
```
5. Импорт схемы в БД: 
```
zcat /usr/share/zabbix-sql-scripts/postgresql/server.sql.gz | sudo -u zabbix psql zabbix
```
6. Конфигурация Zabbix Server
```
sed -i 's/# DBPassword=/DBPassword=123456789/g' /etc/zabbix/zabbix_server.conf
```
7. Конфигурация NGINX
```
sed -i 's/# listen/listen/g' /etc/zabbix/nginx.conf
sed -i 's/# server_name/server_name/g' /etc/zabbix/nginx.conf
```
8. Перезапуск процессов и добавление в загрузку systemd
```
systemctl restart zabbix-server zabbix-agent nginx php8.1-fpm
systemctl enable zabbix-server zabbix-agent nginx php8.1-fpm
```
9. Успешная установка
![Скриншот-1](https://github.com/NatoshFehn/hw-monitoring-02/blob/main/Снимок1.png)
10. Авторизация в админке
![Скриншот-2](https://github.com/NatoshFehn/hw-monitoring-02/blob/main/Снимок2.png)

---

### Задание 2

1. Установка репозитория
```
wget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.0-4%2Bubuntu22.04_all.deb
dpkg -i zabbix-release_6.0-4+ubuntu22.04_all.deb
apt update
```
2. Установка Zabbix-агента
```
apt install zabbix-agent
```
3. Конфигурация агента
```
sed -i 's/Server=127.0.0.1/Server=192.168.0.106/g' /etc/zabbix/zabbix_agentd.conf
```
включаем дебаг, чтобы видеть запросы от сервера:
```
sed -i 's/# DebugLevel=3/DebugLevel=4/g' /etc/zabbix/zabbix_agentd.conf
```
4. Рестарт сервиса и добавление в загрузку systemd
```
systemctl restart zabbix-agent
systemctl enable zabbix-agent
```
5. Скриншот раздела Configuration > Hosts на сервере:
![Скриншот-3](https://github.com/NatoshFehn/hw-monitoring-02/blob/main/Снимок3.png)
6. Скриншот лога zabbix agent с дебагом соединения:
![Скриншот-4](https://github.com/NatoshFehn/hw-monitoring-02/blob/main/Снимок4.png)
7. Скриншот раздела Monitoring > Latest data для обоих хостов, видны поступающие от агентов данные
![Скриншот-5](https://github.com/NatoshFehn/hw-monitoring-02/blob/main/Снимок5.png)
8. Пример сконфигурированного дашборда
![Скриншот-6](https://github.com/NatoshFehn/hw-monitoring-02/blob/main/Снимок6.png)
