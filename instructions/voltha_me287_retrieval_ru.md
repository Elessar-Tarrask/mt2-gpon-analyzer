# Инструкция по получению информации ME 287 от ONT через VOLTHA CLI

## Предварительные требования
- Доступ к кластеру Kubernetes
- Настроенный kubectl с соответствующими правами
- Доступ к подам VOLTHA
- Подключенные и зарегистрированные ONT в системе

## Шаг 1: Подключение к кластеру Kubernetes

1. Проверка текущего контекста Kubernetes:
```bash
sudo kubectl config current-context
```
Пример вывода:
```
kubernetes-admin@cluster.local
```

2. Список доступных подов в пространстве имен default c наименованием voltctl:
```bash
sudo kubectl get pods -n default | grep voltctl
```
Пример вывода:
```
NAME                                    READY   STATUS    RESTARTS   AGE
voltctl-5945467d9-7nrm5                1/1     Running   0          2d
```

## Шаг 2: Доступ к VOLTHA CLI

1. Подключение к поду voltctl:
```bash
sudo kubectl exec -it voltctl-5945467d9-7nrm5 -- /bin/bash
```
Ожидаемый ответ: Вы окажетесь внутри оболочки пода

2. Переход в директорию с voltctl:
```bash
cd /home/voltha
```

3. Проверка доступности voltctl:
```bash
./voltctl --help
```
Пример вывода:
```
Usage:
  voltctl [OPTIONS] <command>

Global Options:
  -c, --config=FILE                     Location of client config file [$VOLTCONFIG]
  -s, --server=SERVER:PORT              IP/Host and port of VOLTHA
  -k, --kafka=SERVER:PORT               IP/Host and port of Kafka
  -e, --kvstore=SERVER:PORT             IP/Host and port of KV store (etcd) [$KVSTORE]
  -a, --apiversion=VERSION[v1|v2|v3]    API version
  -d, --debug                           Enable debug mode
  -t, --timeout=DURATION                API call timeout duration
      --tls                             Use TLS
      --tlscacert=CA_CERT_FILE          Trust certs signed only by this CA
      --tlscert=CERT_FILE               Path to TLS vertificate file
      --tlskey=KEY_FILE                 Path to TLS key file
      --tlsverify                       Use TLS and verify the remote
  -8, --k8sconfig=FILE                  Location of Kubernetes config file [$KUBECONFIG]
      --kvstoretimeout=DURATION         timeout for calls to KV store [$KVSTORE_TIMEOUT]
  -o, --command-options=FILE            Location of command options default configuration file
                                        [$VOLTCTL_COMMAND_OPTIONS]

Help Options:
  -h, --help                            Show this help message

Available commands:
  adapter        adapter commands
  backtrace      backtrace collection
  completion     generate shell compleition
  component      component instance commands
  config         generate voltctl configuration
  device         device commands
  devicegroup    device group commands
  event          event commands
  log            log config commands
  logicaldevice  logical device commands
  message        message commands
  version        display version
```

## Шаг 3: Получение информации ME 287

1. Список всех ONU:
```bash
./voltctl device list
```
Пример вывода:

| ID                          | TYPE              | ROOT  | PARENTID                    | SERIALNUMBER    | CHANNELID | ONUID | MACADDRESS           | ADMINSTATE | OPERSTATUS | CONNECTSTATUS | REASON          | PARENTPORTNO | ADDRESS           |
|-----------------------------|-------------------|-------|-----------------------------|-----------------|-----------|-------|----------------------|------------|------------|---------------|-----------------|--------------|-------------------|
| 691b194a7e8df174d71daebf    | openolt           | true  | 504594017480                | 742313706651    | 0         | 0     | 50:45:94:01:74:80    | ENABLED    | ACTIVE     | REACHABLE     |                 | 0            | 10.231.31.11:9191 |
| 8bb529d1693eedfd8649317d    | brcm_openomci_onu | false | 691b194a7e8df174d71daebf    | BDCM6BC3FCF3    | 13        | 126   |                      | ENABLED    | ACTIVE     | REACHABLE     | omci-flows-pushed | 536870925    | unknown          |
| 5622f0479c40c6b87e7c7b5d    | brcm_openomci_onu | false | 691b194a7e8df174d71daebf    | BDCM6BC3F8D0    | 19        | 47    |                      | ENABLED    | ACTIVE     | REACHABLE     | omci-flows-pushed | 536870931    | unknown          |

В выводе команды отображаются:
1. OLT (Optical Line Terminal):
   - ID: 691b194a7e8df174d71daebf
   - Тип: openolt
   - MAC-адрес: 50:45:94:01:74:80
   - IP-адрес: 10.231.31.11:9191

2. Две ONU (Optical Network Unit):
   - ID: 8bb529d1693eedfd8649317d и 5622f0479c40c6b87e7c7b5d
   - Тип: brcm_openomci_onu
   - Серийные номера: BDCM6BC3FCF3 и BDCM6BC3F8D0
   - Каналы: 13 и 19
   - ONU ID: 126 и 47
   - Статус: ENABLED, ACTIVE, REACHABLE
   - Причина: omci-flows-pushed (потоки OMCI настроены)

Все устройства находятся в рабочем состоянии (ENABLED, ACTIVE, REACHABLE), что указывает на успешное подключение и настройку.

2. Для каждой ONU получить информацию ME 287:
```bash
./voltctl device omci get 8bb529d1693eedfd8649317d 287
```
Пример вывода:
```
ME 287 (ONU2-G) Information:
- Entity Instance: 0
- Attributes:
  - Attribute 1 (Managed entity ID): 287
  - Attribute 2 (Vendor ID): 0x0000
  - Attribute 3 (Version): 0x0001
  - Attribute 4 (Serial number): 0x0000000000000000
  - Attribute 5 (Traffic management option): 0x01
  - Attribute 6 (Priority queue): 0x01
  - Attribute 7 (Total T-CONT buffer number): 0x08
  - Attribute 8 (Total priority queue number): 0x08
  - Attribute 9 (Total traffic scheduler number): 0x08
  - Attribute 10 (Deprecated): 0x00
  - Attribute 11 (Total GEM port-ID number): 0x80
  - Attribute 12 (Sysuptime): 0x00000000
  - Attribute 13 (Connectivity capability): 0x01
  - Attribute 14 (Current connectivity mode): 0x01
  - Attribute 15 (QoS configuration flexibility): 0x01
  - Attribute 16 (Priority queue scale factor): 0x01
```

## Шаг 4: Сохранение вывода

1. Сохранение вывода в файл:
```bash
./voltctl device omci get 8bb529d1693eedfd8649317d 287 > me287_8bb529d1693eedfd8649317d_20240312_1430.txt
```

2. Копирование файла с пода на локальную машину:
```bash
kubectl cp voltha/voltctl-5945467d9-7nrm5:me287_8bb529d1693eedfd8649317d_20240312_1430.txt ./me287_8bb529d1693eedfd8649317d_20240312_1430.txt
```

## Шаг 5: Анализ нескольких ONT

Для получения информации ME 287 от всех ONT в системе:

```bash
for device in $(./voltctl device list | grep "brcm_openomci_onu" | awk '{print $1}'); do
    ./voltctl device omci get $device 287 > me287_$device_$(date +%Y%m%d_%H%M%S).txt
done
```

## Устранение неполадок

1. Если устройство не отвечает:
```bash
./voltctl device state 8bb529d1693eedfd8649317d
```
Возможные выводы:
```
ADMIN STATE: ENABLED
OPER STATUS: ACTIVE
CONNECT STATUS: REACHABLE
REASON: omci-flows-pushed
```
или
```
ADMIN STATE: DISABLED
OPER STATUS: FAILED
CONNECT STATUS: UNREACHABLE
REASON: device-not-found
```

2. Проверка статуса OMCI коммуникации:
```bash
./voltctl device omci stats 8bb529d1693eedfd8649317d
```
Пример вывода:
```
OMCI Statistics:
- Total messages sent: 100
- Total messages received: 100
- Success rate: 100%
- Last error: None
- Last error time: N/A
```

3. Сброс OMCI коммуникации:
```bash
device 0001aabbccddeeff omci reset
```
Ожидаемый ответ:
```
OMCI communication reset successfully
```

## Распространенные сообщения об ошибках и решения

1. "Device not found":
   - Проверьте правильность ID устройства
   - Убедитесь, что ONU правильно зарегистрирована
   - Проверьте состояние устройства

2. "OMCI communication failed":
   - Проверьте подключение ONU
   - Убедитесь, что канал OMCI правильно настроен
   - Проверьте наличие сетевых проблем

3. "Permission denied":
   - Проверьте права доступа в Kubernetes
   - Проверьте права доступа к VOLTHA CLI
   - Убедитесь в правильном доступе к пространству имен

## Понимание информации ME 287

При выполнении команды `device 0001aabbccddeeff omci get 287` вы получаете информацию о управляемой сущности ONU2-G (ME 287). Эта ME является критически важной, так как предоставляет информацию о возможностях и поддерживаемых функциях ONT. Вот как интерпретировать эту информацию:

### Ключевые атрибуты и их значение

1. **Атрибут 1 (Managed entity ID)**: 287
   - Подтверждает, что вы смотрите на ME 287 (ONU2-G)

2. **Атрибут 2 (Vendor ID)**: 0x0000
   - Идентифицирует производителя ONT
   - Примеры значений:
     - 0x0000: Общий
     - 0x0001: Производитель A
     - 0x0002: Производитель B

3. **Атрибут 3 (Version)**: 0x0001
   - Указывает версию реализации ONU2-G
   - Более высокие версии могут поддерживать дополнительные функции

4. **Атрибут 5 (Traffic management option)**: 0x01
   - Указывает поддерживаемые функции управления трафиком
   - Значения:
     - 0x01: Базовое управление трафиком
     - 0x02: Расширенное управление трафиком
     - 0x03: Полное управление трафиком

5. **Атрибут 7 (Total T-CONT buffer number)**: 0x08
   - Указывает количество доступных буферов T-CONT
   - Каждый T-CONT представляет контейнер для восходящего трафика

6. **Атрибут 8 (Total priority queue number)**: 0x08
   - Показывает количество поддерживаемых приоритетных очередей
   - Важно для конфигурации QoS (Quality of Service)

7. **Атрибут 9 (Total traffic scheduler number)**: 0x08
   - Указывает количество доступных планировщиков трафика
   - Используется для формирования и планирования трафика

8. **Атрибут 11 (Total GEM port-ID number)**: 0x80
   - Показывает максимальное количество поддерживаемых GEM портов
   - GEM порты используются для передачи данных

9. **Атрибут 13 (Connectivity capability)**: 0x01
   - Указывает поддерживаемые режимы подключения
   - Значения:
     - 0x01: Базовое подключение
     - 0x02: Расширенное подключение
     - 0x03: Полное подключение

### Определение поддерживаемых ME

Для определения, какие ME поддерживаются ONT, необходимо:

1. Проверить возможности ONU через атрибуты ME 287
2. Запросить конкретные ME для проверки поддержки

Примеры команд для проверки конкретных ME:

```bash
# Проверка поддержки ME 2 (ONU Data)
device 0001aabbccddeeff omci get 2

# Проверка поддержки ME 11 (PPTP Ethernet UNI)
device 0001aabbccddeeff omci get 11

# Проверка поддержки ME 45 (MAC Bridge Service Profile)
device 0001aabbccddeeff omci get 45
```

### Интерпретация поддержки ME

1. **Успешный ответ**:
   - Если получен валидный ответ с атрибутами, ME поддерживается
   - Пример:
     ```
     ME 11 (PPTP Ethernet UNI) Information:
     - Entity Instance: 1
     - Attributes:
       - Attribute 1: 0x01
       ...
     ```

2. **Ответ с ошибкой**:
   - Если получена ошибка "ME not supported", ONT не поддерживает эту ME
   - Пример:
     ```
     Error: ME not supported
     ```

### Распространенные ME и их индикаторы поддержки

1. **Базовые ME** (обычно поддерживаются всеми ONT):
   - ME 2 (ONU Data)
   - ME 7 (Physical Path Termination Point)
   - ME 11 (PPTP Ethernet UNI)
   - ME 45 (MAC Bridge Service Profile)

2. **Расширенные ME** (могут не поддерживаться всеми ONT):
   - ME 84 (VLAN Tagging Filter Data)
   - ME 171 (Extended VLAN Tagging Operation)
   - ME 263 (ANI-G)
   - ME 287 (ONU2-G)

### Практический пример

Допустим, вы хотите проверить поддержку VLAN тегирования:

1. Сначала проверьте возможности ME 287:
```bash
device 0001aabbccddeeff omci get 287
```

2. Затем проверьте конкретные ME, связанные с VLAN:
```bash
# Проверка VLAN Tagging Filter Data (ME 84)
device 0001aabbccddeeff omci get 84

# Проверка Extended VLAN Tagging (ME 171)
device 0001aabbccddeeff omci get 171
```

3. Интерпретация результатов:
   - Если обе ME возвращают валидную информацию, ONT поддерживает VLAN тегирование
   - Если получены ошибки "ME not supported", ONT не поддерживает эти функции

### Примечания по поддержке ME
- Не все ONT поддерживают все ME, определенные в G.988
- Поддержка ME может варьироваться в зависимости от производителя и модели ONT
- Некоторые ME могут поддерживаться, но с ограниченной функциональностью
- Всегда проверяйте поддержку ME через фактические запросы, а не предполагайте на основе информации ME 287

## Ссылки
- Стандарт G.988 для спецификаций ME 287
- Документация VOLTHA по CLI командам
- Документация Kubernetes по доступу к подам 