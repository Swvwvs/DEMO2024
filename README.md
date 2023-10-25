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
Получаию информацию о подключении 
![image](https://github.com/Swvwvs/DEMO2024/assets/148449545/ddcc3b07-089c-4e9b-a345-6f9bdcabec59)
