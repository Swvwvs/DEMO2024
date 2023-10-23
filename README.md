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
|                |ens225              |192.168.0.166   |/30             |                      |
|HQ-R            |ens192              |192.168.0.161   |/30             |192.168.0.162         |
|                |ens224              |192.168.0.1     |/25             |                      |
|BR-R            |ens192              |192.168.0.1     |/30             |192.168.0.161         |
|                |ens224              |192.168.0.129   |/27             |                      |
|HQ-SRV          |ens192              |192.168.0.126   |/25             |192.168.0.1           |
|BR-SRV          |ens192              |192.168.0.158   |/27             |192.168.0.129         |

## 1. Настройка интерфейсов
#### посмотрел существующие интрфесы с помощью команды
```
ip a
```
