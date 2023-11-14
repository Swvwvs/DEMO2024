# DEMO 2024
## №1.
## Описание задания
Выполните базовую настройку всех устройств:

- Соберите топологию согласно рисунку. Все устройства работают на OC Linux - Debian.
- Присвоить имена в соответствии с топологией.
- Рассчитайте IP-адресацию IPv4 и IPv6. Необходимо заполнить таблицу №1. При необходимости отредактируйте таблицу.
- адресов для сети офиса BRANCH - не более 16. Для IPv6 пропустите этот пункт
- Пул адресов для сети офиса HQ - не более 64. Для IPv6 пропустите этот пункт.

## Топология
![image](https://github.com/Swvwvs/DEMO2024/assets/148449545/3443c905-88f6-4522-bd3a-71863d9af5f3)

## Таблица №1 (разбил сеть на подсети)
|Имя устройства  |Интерфейс           |IPv4            |Маска/Префикс   |Шлюз            |
|  ------------- | -------------      | -------------  |  ------------- |  -------------       |
|ISP             |ens192              |10.12.24.10     |/24             |10.12.24.254          |
|                |ens224              |192.168.0.162   |/30             |                      |
|                |ens256              |192.168.0.166   |/30             |                      |
|HQ-R            |ens192              |192.168.0.161   |/30             |192.168.0.162         |
|                |ens224              |192.168.0.1     |/25             |                      |
|BR-R            |ens192              |192.168.0.165   |/30             |192.168.0.166         |
|                |ens224              |192.168.0.129   |/27             |                      |
|HQ-SRV          |ens192              |192.168.0.126   |/25             |192.168.0.1           |
|BR-SRV          |ens192              |192.168.0.158   |/27             |192.168.0.129         |

## 1. Настройка интерфейсов
#### Посмотрел существующие интрфесы
```
ip a
```
#### Зашёл в файл настройки конфигурации интерфесов
```
nano /etc/network/interfaces
```
#### Назначил IP на интерфейсы в соответствии с таблицей адресации
  #### ISP
```
auto ens192
iface ens192 inet static
address 10.12.24.10
netmask 255.255.255.0
gateway 10.12.24.254

auto ens224
iface ens224 inet static
address 192.168.0.166
netmask 255.255.255.252

auto ens256 
iface ens256 inet static
address 192.168.0.162
netmask 255.255.255.252
```
#### HQ-R
```
auto ens192
iface ens192 inet static
address 192.168.0.161
netmask 255.255.255.252
gateway 192.168.0.162

auto ens224
iface ens224 inet static
address 192.168.0.1
netmask 255.255.255.128
```
#### BR-R
```
auto ens192
iface ens192 inet static
address 192.168.0.165
netmask 255.255.255.252
gateway 192.168.0.166

auto ens224
iface ens224 inet static
address 192.168.0.129
netmask 255.255.255.224
```
#### HQ-SRV
```
auto ens192
iface ens192 inet static
address 192.168.0.126
netmask 255.255.255.128
gateway 192.168.0.1
```
#### BR-SRV
```
auto ens192
iface ens192 inet static
address 192.168.0.158
netmask 255.255.255.224
gateway 192.168.0.129
```
#### Сохранил конфигурацию комбинацией клавишь
```
Ctrl+S
```
#### Вышел из конфгурационного файла комбинацией клавишь
```
Ctrl+X
```
#### Перезагрузил Сервис работы с сетью
```
Systemctl restart networking.service
```
***По аналогии делаем все тоже самое и на других виртуальных машинах.***

## №1.2
## Описание задания
- Настройте внутреннюю динамическую маршрутизацию по средствам FRR. Выберите и обоснуйте выбор протокола динамической маршрутизации из расчёта, что в дальнейшем сеть будет масштабироваться.
### Установка пакета frr
```
apt-get install frr
```
### После установки frr проверил его состояние
```
systemctl status frr
```  
![image](https://github.com/Swvwvs/DEMO2024/assets/148449545/30a2d62a-4480-4427-bc38-e8edbcbe8db7)
### Зашёл в файл
```
nano /etc/frr/daemons
```
### Изменил значение ospfd
```
ospfd=yes
```
### Перезагрузил службу frr
```
systemctl restart frr
```
### Зашёл в настройку маршрутизации на ISP через команду 
```
vtysh
```
### Посмотрел IP адреса и их состояние
```
show ip interface brief
```
### Зашёл в конфигурацию терминала 
```
conf t
```
### Запустил процесс OSPF
```
router ospf
```
### Внёс интерфейсы
```
network 192.168.0.162/30 area 0
network 192.168.0.166/30 area 0
```
### Посмотрел соседей
```
do show ip ospf neighbor
```  
![image](https://github.com/Swvwvs/DEMO2024/assets/148449545/5b946b34-545d-4d34-b7b2-8c24af65820d)  
### Сохранил конфигурацию
```
copy running-config startup-config
```  
***По аналогии делаем все тоже самое и на виртуальных машинах HQ-R и BR-R.***

## №1.4
#### Настроить локальные учётные записи на всех устройствах в соответствии с таблицей
|Учётная запись  |Пароль         |Примечания      |
|  ------------- | ------------- | -------------  |
|Admin           |P@ssw0rd       |ISP HQ-SRV HQ-R |
|Branch admin    |P@ssw0rd       |BR-SRV BR-R     |
|Network admin   |P@ssw0rd       |HQ-R BR-R BR-SRV|

#### Зашёл на ISP создал пользователя и задал пароль в соответствии с таблицей
Добавил пользователя
```
useradd Admin
```
Задал пароль созданному пользователю Admin
```
passwd Admin
пароль:P@ssw0rd
повторите ввод пароля:P@ssw0rd
```
Посмотрел созданного пользователя
```
nano /etc/passwd
```
![image](https://github.com/Swvwvs/DEMO2024/assets/148449545/9aa73d01-5772-42b6-89bc-6f6b4543a9a2)  
***По аналогии делаем все тоже самое и на виртуальных машинах по таблице***
## №00
#### Установил Serial Port на виртуальную машину
1. Выключил виртуальную машину
2. Открыл Edit settings
3. Нажал Add other device
4. Выбрал Serial port
5. Изменил Use output file на Use network у serial port 1
6. Назначил номер порту  
![image](https://github.com/Swvwvs/DEMO2024/assets/148449545/9644ca07-8e54-4034-856a-33fb80c93182)
7. Включил виртуальную машину
#### Powershell (cmd)
Зашёл в свой ESXi с помощью команды 
```
ssh -l root 10.12.24.5
```
Ввёл пароль
Посмотрел список правил Firewall
```
esxcli network firewall ruleset list
```
Включил правила подключения SerialPort
```
esxcli network firewall ruleset set --enabled=true --ruleset-id=remoteSerialPort
```
Посмотрел изменения в списке правил Firewall  
![image](https://github.com/Swvwvs/DEMO2024/assets/148449545/b52ae0fa-3f34-499e-a007-a165b79c96ef)
#### Настройка службы подключения
Ввёл команду для запуска службы
```
systemctl start serial-getty@ttyS0.service
```
Ввёл команду для автозапуска скрипта
```
systemctl enable serial-getty@ttyS0.service
```
#### PuTTY
Открыл PuTTY
Ввёл свой IP и номер порта 
в type connection выбрал other  
![image](https://github.com/Swvwvs/DEMO2024/assets/148449545/41c56534-0b65-44f3-b453-fda6cc08cee7)  
нажал подключиться
## №1.5
## устновка iperf3 и измерение пропускной способности между двумя узлами HQ-R и ISP
Уставил iperf3 на обоих машинах
```
apt-get install iperf3
```
Установил ufw
```
apt-get install ufw
```
Назначил порт 5201 на обоих машинах
```
ufw allow 5201
```
Запустил серверную часть программы на ISP
```
iperf3 -s
```
Подключаюсь на HQ-R к серверу ISP
```
iperf3 -c 192.168.0.162 -f K
```
Получаю информацию о подключении   
![image](https://github.com/Swvwvs/DEMO2024/assets/148449545/ddcc3b07-089c-4e9b-a345-6f9bdcabec59)
## №1.6
#### Backup скрипт для сохранения конфигурации сетевый устройств BR-R HQ-R
Создал каталог, в который будут сохранятся Backup-ы
```
mkdir /etc/frr/backup
```
Установил rsync 
```
apt-get install rsync
```
Зашёл в список скриптов rsync
```
crontab -e
```
Внёс скрипт для создания Backup-а 
```
0 0 * * * rsync -avzh /etc/frr/frr.conf /etc/frr/backup
```
> Первый адрес задаем файл, из которого будет делаться Backup  
> Второй адрес задаем каталог в который будет сохраняться Backup
Сохранил внесённые изменения в файл комбинацией клавиш
```
Ctrl+S
```
Вышел из файла комбинацией клавиш
```
Ctrl+X
```
#### Проверка работы Backup скрипта
> Через какое то время Backup файла должен появится в каталоге, который мы указывали в скрипте
Захожу в каталог, который я указывал в скрипте
```
cd /etc/frr/backup
```
Смотрю имеющиеся файлы в каталоге
```
ls -a
```
Убеждаюсь что скрипт сработал  
![image](https://github.com/Swvwvs/DEMO2024/assets/148449545/e7cf7f95-fdb8-4370-8998-23fe97a57162)  
***файл Backup-а находится в каталоге***
## №2.1
#### DNS-сервер на HQ-SRV с прямой и обратной зоной
|Имя             |Тип записи          |Адрес           |
|  ------------- | -------------      | -------------  |
|hq-r.hq.work    |A, PTR              |IP-адрес        |
|hq-srv.hq.work  |A, PTR              | IP-адрес       |  

|Имя             |Тип записи          |Адрес           |
|  ------------- | -------------      | -------------  |
|br-r.br.work    |A, PTR              |IP-адрес        |
|br-srv.br.work  |A                   | IP-адрес       |  

Установил bind9 с доп.пакетами
```
apt-get install bind9 bind9utils bind9-doc
```
Настроил режим IPv4
```
nano /etc/default/named
```
Ввёл 
```
OPTIONS="-u bind -4"
```
Зашёл в resolv.conf
```
nano /etc/resolv.conf
```
Ввёл IP интерфейса HQ-srv
```
nameserver 192.168.0.126
```
Открыл файл `/etc/bind/named.conf.options`
В блок `options` внес:
```
listen-on { 127.0.0.1; 192.168.0.126; };
listen-on-v6 { none; };
allow-query { any; };
forward first;
forwarders { 8.8.8.8;};
```
Прописал зоны в файле `/etc/bind/named.conf.default-zones`:
```
zone "hq.work" {
  type master;
  file "/etc/bind/db.hq";
};

zone "br.work" {
  type master;
  file "/etc/bind/db.local";
};

zone "0.168.192.in-addr.arpa" {
  type master;
  file "/etc/bind/0.168.192.in-addr.arpa.zone";
};
```
Создал файл прямой зоны для офиса **HQ**, скопировав файл **`/etc/bind/db.127`**. Назвал его **`db.hq`**:
```
cp /etc/bind/db.127 /etc/bind/db.hq
```
Создал файл прямой зоны для офиса **BR**, скопировав файл **`/etc/bind/db.127`**. Назвал его **`db.local`**:
```
cp /etc/bind/db.127 /etc/bind/db.local
```
Cкопировал файл **`/etc/bind/db.127`**:
```
cp /etc/bind/db.127 /etc/bind/0.168.192.in-addr.arpa.zone
```
Заполнил файл прямой зоны **HQ**
```
;
; BIND data file for local loopback interface
;
$TTL  604800
@         IN    SOA    hq.work.  root.hq.work.  (
                        3    ; Serial
                   604800    ; Refresh
                    86400    ; Retry
                  2419200    ; Expire
                   604800    ; Negative Cache TTL
;
@         IN    NS    localhost.
@         IN    A     192.168.0.126
@         IN    AAAA  ::1
hq-srv    IN    A    192.168.0.126
hq-r      IN    A    192.168.0.1
```
Заполнил файл прямой зоны **BR**
```
;
; BIND data file for local loopback interface
;
$TTL  604800
@         IN    SOA    branch.work.  root.branch.work.  (
                        3    ; Serial
                   604800    ; Refresh
                    86400    ; Retry
                  2419200    ; Expire
                   604800 )  ; Negative Cache TTL
;
@         IN    NS    localhost.
@         IN    A     192.168.0.126
@         IN    AAAA  ::1
br-srv    IN    A    192.168.0.158
br-r      IN    A    192.168.0.129
```
Заполнил файл обратной зоны
```
;
; BIND reverse data file for local loopback interface
;
$TTL  604800
@         IN    SOA    work.  root.work.  (
                        3    ; Serial
                   604800    ; Refresh
                    86400    ; Retry
                  2419200    ; Expire
                   604800 )  ; Negative Cache TTL
;
@         IN    NS    localhost.
1         IN    PTR   hq-r.hq.work.
126       IN    PTR   hq-srv.hq.work.
129       IN    PTR   br-r.br.work.
158       IN    PTR   br-srv.br.work.
```
Проверил работоспособность прямой зоны HQ и BR:
```
nslookup hq-r.hq.work
```
![image](https://github.com/Swvwvs/DEMO2024/assets/148449545/fc6595d6-0efd-46e4-9e12-eaf44406e627)  
```
nslookup br-r.br.work
```
![image](https://github.com/Swvwvs/DEMO2024/assets/148449545/ef194181-731e-4a2a-bdb7-b442814c93ed)  
Проверил обратную зону:
```
nslookup 192.168.0.126
```
![image](https://github.com/Swvwvs/DEMO2024/assets/148449545/6a2e595f-45c5-4c46-94f1-d3cb45ff93f0)  
```
nslookup 192.168.0.158
```
![image](https://github.com/Swvwvs/DEMO2024/assets/148449545/288a8f18-78bd-484b-b707-b155e9352aa3)
С помощью команды named-checkconf можно проверить синтаксис файлов named на ошибки:
```
named-checkconf
```
