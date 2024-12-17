# Домашнее задание к занятию 1 «Disaster recovery и Keepalived»
*Асадбеков Асадбек*

## Задание 1

* Дана схема для *Cisco Packet Tracer*, рассматриваемая в лекции.
* На данной схеме уже настроено отслеживание интерфейсов маршрутизаторов Gi0/1 (для нулевой группы)
* Необходимо аналогично настроить отслеживание состояния интерфейсов Gi0/0 (для первой группы).
* Для проверки корректности настройки, разорвите один из кабелей между одним из маршрутизаторов и Switch0 и запустите ping между PC0 и Server0.
* На проверку отправьте получившуюся схему в формате pkt и скриншот, где виден процесс настройки маршрутизатора.

![](path_to_screenshot1.png)
![](path_to_screenshot1.png)

## Задание 2
* Запустите две виртуальные машины Linux, установите и настройте сервис Keepalived как в лекции, используя пример конфигурационного файла.
* Настройте любой веб-сервер (например, nginx или simple python server) на двух виртуальных машинах
* Напишите Bash-скрипт, который будет проверять доступность порта данного веб-сервера и существование файла index.html в root-директории данного веб-сервера.
* Настройте Keepalived так, чтобы он запускал данный скрипт каждые 3 секунды и переносил виртуальный IP на другой сервер, если bash-скрипт завершался с кодом, отличным от нуля (то есть порт веб-сервера был недоступен или отсутствовал index.html). Используйте для этого секцию vrrp_script
* На проверку отправьте получившейся bash-скрипт и конфигурационный файл keepalived, а также скриншот с демонстрацией переезда плавающего ip на другой сервер в случае недоступности порта или файла index.html

Bash-скрипт: `/etc/keepalived/check_nginx.sh`

```bash
#!/bin/bash

PORT=80
ROOT_DIR="/var/www/html"
INDEX_FILE="$ROOT_DIR/index.html"

# Проверка доступности порта
if ! /usr/bin/nc -z localhost $PORT; then
    exit 1
fi

# Проверка существования index.html
if [ ! -f "$INDEX_FILE" ]; then
    exit 1
fi

exit 0
```

Конфигурационный файл Keepalived: `/etc/keepalived/keepalived.conf`

```bash
global_defs {
    script_user root
    enable_script_security
    log-detail
}

vrrp_instance VI_1 {
    state MASTER
    interface enp0s3
    virtual_router_id 15
    priority 255
    advert_int 1

    virtual_ipaddress {
        192.168.1.200/24
    }

    track_script {
        chk_nginx
    }
}

vrrp_script chk_nginx {
    script "/etc/keepalived/check_nginx.sh"
    interval 3
    weight -10
}

![](path_to_screenshot3.png)
![](path_to_screenshot3.png)
![](path_to_screenshot3.png)
![](path_to_screenshot3.png)
