# Установка ОС и настройка

1. Для установки был выбран Ubuntu Server 24.04. ISO-файл был скачан с официального сайта.
2. Задание было выполнено в VirtualBox 7.2.8, ОС хоста - Windows 11.
3. Были заданы следующие настройки VM:
    1. 2 vCPU
    2. 4096 MB RAM
    3. 30 GB диск
    4. Сеть - NAT с port-forwarding (2222(host)->22, 8080(host)->80)
4. При создании VM в настройках был указан образ гостевой ОС.
5. При первом запуске VM была проведена установка с настройками по-умолчанию, за исключением:
    1. Был создан пользователь с ником onlymiind и паролем 123
    2. Была выбрана опция установки пакета OpenSSH. Ключи были получены из аккаунта в Github (через установщик, по нику).
6. После установки и перезагрузки было проведено обновление системы командой ```sudo apt update``` и еще одна перезагрузка.
7. Был включен сетевой экран (использовался ufw) командами: ```sudo ufw allow ssh``` ```sudo ufw allow 80/tcp```. Итог настройки представлен ниже:

```
onlymiind@onlymiind:~$ sudo ufw status verbose
[sudo] password for onlymiind:
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), deny (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere
80/tcp                     ALLOW IN    Anywhere
22/tcp (v6)                ALLOW IN    Anywhere (v6)
80/tcp (v6)                ALLOW IN    Anywhere (v6)
```
8. Были установлены пакеты ```docker.io``` и ```docker-compose```
9. Пользователь был добавлен в группу docker командой ```sudo usermod -a -G docker onlymiind```
10. Была создана директория ```app``` в домашней директории пользователя VM.
11. Файлы ```.env```, ```nginx.conf``` и ```docker-compose.yaml``` были скопированы на VM командой ```scp -P 2222 .\<имя файла> onlymiind@localhost:/home/onlymiind/app/<имя файла>```
12. Compose-проект был запущен командой ```sudo docker compose up```

## hostnamectl

```
onlymiind@onlymiind:~$ hostnamectl
 Static hostname: onlymiind
       Icon name: computer-vm
         Chassis: vm 🖴
      Machine ID: d3166952d602410888d9131f4d86151e
         Boot ID: fd18821d9bf0415497d4129fe8d98750
  Virtualization: oracle
Operating System: Ubuntu 24.04.4 LTS
          Kernel: Linux 6.8.0-124-generic
    Architecture: x86-64
 Hardware Vendor: innotek GmbH
  Hardware Model: VirtualBox
Firmware Version: VirtualBox
   Firmware Date: Fri 2006-12-01
    Firmware Age: 19y 6month 1w
```

## ip a

```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:24:51:39 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 metric 100 brd 10.0.2.255 scope global dynamic enp0s3
       valid_lft 85927sec preferred_lft 85927sec
    inet6 fd17:625c:f037:2:a00:27ff:fe24:5139/64 scope global dynamic mngtmpaddr noprefixroute
       valid_lft 86186sec preferred_lft 14186sec
    inet6 fe80::a00:27ff:fe24:5139/64 scope link
       valid_lft forever preferred_lft forever
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default
    link/ether c6:f2:3c:e4:67:77 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
4: br-f1da11f3115c: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default
    link/ether fa:6f:5d:ac:3c:0b brd ff:ff:ff:ff:ff:ff
    inet 172.18.0.1/16 brd 172.18.255.255 scope global br-f1da11f3115c
       valid_lft forever preferred_lft forever
```

## Подключение по SSH

```
PS C:\Users\sasha> ssh -p 2222 onlymiind@127.0.0.1
Enter passphrase for key 'C:\Users\sasha/.ssh/id_ed25519':
Welcome to Ubuntu 24.04.4 LTS (GNU/Linux 6.8.0-124-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Mon Jun  8 02:59:01 PM UTC 2026

  System load:             0.07
  Usage of /:              47.8% of 13.67GB
  Memory usage:            7%
  Swap usage:              0%
  Processes:               110
  Users logged in:         0
  IPv4 address for enp0s3: 10.0.2.15
  IPv6 address for enp0s3: fd17:625c:f037:2:a00:27ff:fe24:5139


Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

1 additional security update can be applied with ESM Apps.
Learn more about enabling ESM Apps service at https://ubuntu.com/esm


Last login: Mon Jun  8 14:50:46 2026 from 10.0.2.2
```

## docker

```
onlymiind@onlymiind:~$ docker --version
Docker version 29.1.3, build 29.1.3-0ubuntu3~24.04.2
onlymiind@onlymiind:~$ docker compose version
Docker Compose version 2.40.3+ds1-0ubuntu1~24.04.1
onlymiind@onlymiind:~$
```

## docker compose up -d, docker compose ps
```
onlymiind@onlymiind:~/app$ docker compose up -d
[+] Running 3/3
 ✔ Container app-app-1    Started                                                                                  1.1s
 ✔ Container app-db-1     Started                                                                                  1.0s
 ✔ Container app-proxy-1  Started                                                                                  1.2s
onlymiind@onlymiind:~/app$ docker compose ps
NAME          IMAGE            COMMAND                  SERVICE   CREATED          STATUS                            PORTS
app-app-1     traefik/whoami   "/whoami"                app       36 minutes ago   Up 6 seconds                      80/tcp
app-db-1      postgres:15      "docker-entrypoint.s…"   db        36 minutes ago   Up 6 seconds (health: starting)   0.0.0.0:5432->5432/tcp, [::]:5432->5432/tcp
app-proxy-1   nginx:1-alpine   "/docker-entrypoint.…"   proxy     36 minutes ago   Up 6 seconds                      0.0.0.0:80->80/tcp, [::]:80->80/tcp
```

## curl -i "http://localhost:8080" (на хосте)

```
PS C:\Users\sasha\projects\container> curl -i "http://localhost:8080"
HTTP/1.1 200 OK
Server: nginx/1.31.1
Date: Mon, 08 Jun 2026 15:03:33 GMT
Content-Type: text/plain; charset=utf-8
Content-Length: 156
Connection: keep-alive

Hostname: df9045ef6dac
IP: 127.0.0.1
IP: ::1
IP: 172.18.0.3
RemoteAddr: 172.18.0.4:36880
GET / HTTP/1.1
Host: app
User-Agent: curl/8.19.0
Accept: */*

```
