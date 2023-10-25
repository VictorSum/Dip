# Дипломная работа. Виктор Сумской.

# Доступ Kibana: 84.201.150.187:5601
# Доступ Grafana: 84.201.144.119:3000 login: admin pass: 12345

# Задание можно посмотреть по ссылке:
## [SYS-DIPLOM](https://github.com/netology-code/sys-diplom)

# Все конфигурационные файлы в репозитории. 

# 1. С помощью Terraform создаётся инфраструктура 
[main.tf](https://github.com/Dip/blob/main/main.tf)
Сеть с четырьмя подсетями в трёх зонах доступности. Реализуется файервол разрешающий входящий трафик только к определённым портам.

![subnet](https://github.com/VictorSum/Dip/blob/main/img/subnets.png)
# Балансировщик:
![balancer](https://github.com/VictorSum/Dip/blob/main/img/balancer_1.png)
# Целевые группы:
![targetgroups](https://github.com/VictorSum/Dip/main/img/balancer_2.png)
# Группы безопасности:
![backendgroups](https://github.com/VictorSum/Dip/blob/main/img/security.png)
# 2. Далее с помощью ansible проверяем доступность узлов ansible –i inventory.ini all -m ping.
![](https://github.com/VictorSum/Dip/blob/main/img/ansible_ping.png)

Все узлы доступны, последний это наш балансировщик.

![](https://github.com/VictorSum/Dip/blob/main/img/outputs.png)

# 3. Далее с помощью плейбуков устанавливается сервисы на соответсвующие ВМ - ansible-playbook -i inventory.ini --private-key=/home/som/.ssh/id_ed25519 prometheus.yml grafana.yml web-notls.yml node-exporter.yml nginxlog-exporter.yml elastic.yml kibana.yml filebeat.yml
![](https://github.com/VictorSum/Dip/blob/main/img/yandex_vms.png)

ВМ "project" 51.250.38.230 моя машина с проектом.

# 4. Cайт.

Две ВМ vm-nginx-1 и vm-nginx-2 для web-серверов во внутренней сети. Установлен nginx с нашим сайтом. Заходим через bastion по ssh. Отображается запущенные nginx и filebeat.

![](https://github.com/VictorSum/Dip/blob/main/img/ssh_1.png)

![](https://github.com/VictorSum/Dip/blob/main/img/ssh_2.png)

Публичный ip балансировщика открывает сайт. Конфигурация плейбука устанавливающий nginx и сайт взята из книги "Запускаем ansible" с небольшими доработками.
158.160.114.43:80 
![](https://github.com/VictorSum/Dip/blob/main/img/ngnix.png)

# 5. Мониторинг.
Проверяем работу Prometheus ip 10.0.3.3.
Сбор метрик.
![](https://github.com/VictorSum/Dip/blob/main/img/metrics.png)

Проверяем работу сервиса node-exporter.
![](https://github.com/VictorSum/Dip/blob/main/img/node.png)

Переходим к Grafana. ip  84.201.169.56:3000.

Основные метрики с обоих серверов nginx1, nginx2 - CPU, RAM, диски, сеть, http_response_count_total, http_response_size_bytes
Перед этим загружаем подготовленный дашборд описаный в json файле.
![](https://github.com/VictorSum/Dip/blob/main/img/dashboard.png)

![](https://github.com/VictorSum/Dip/blob/main/img/grafana_1.png)

![](https://github.com/VictorSum/Dip/blob/main/img/grafana_2.png)

![](https://github.com/VictorSum/Dip/blob/main/img/grafana_3.png)

Добавление tresholds.

![](https://github.com/VictorSum/Dip/blob/main/img/tresholds.png)

![](https://github.com/VictorSum/Dip/blob/main/img/tresholds_2.png)

![](https://github.com/VictorSum/Dip/blob/main/img/tresholds_3.png)

Заходим в Kibana. ip 84.201.170.82:5601
![](https://github.com/VictorSum/Dip/blob/main/img/kibana_1.png)

![](https://github.com/VictorSum/Dip/blob/main/img/kibana_2.png)

![](https://github.com/VictorSum/Dip/blob/main/img/kibana_3.png)

# 6. Резервное копирование. 
Создаем snapshot дисков всех ВМ. Ограничиваем время жизни snaphot в неделю. Сами snaphot настраиваем на ежедневное копирование.
Расписание.
![](https://github.com/VictorSum/Dip/blob/main/img/snapshot.png)

# Правки: [main.tf](https://github.com/AlexanderSomkin/diplom/blob/main/main.tf)

Автоматизация дашбордов: 

![](https://github.com/VictorSum/Dip/main/img/640f030c-f0e7-489b-bd12-690a01629f51.jpg)



