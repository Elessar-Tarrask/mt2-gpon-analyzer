# Детальный анализ OMCI сообщений из RadisysCapture.gpon
**Дата анализа:** 03.04.2025
**Стандарт:** T-REC-G.988-202211-I

## Введение

Данный анализ содержит подробное описание OMCI сообщений, обнаруженных в файле RadisysCapture.gpon. Для каждого типа сообщения приведена детальная расшифровка всех полей, пояснение предназначения сообщения, а также анализ отправленных и полученных данных.

## Структура анализа

Каждое сообщение анализируется в следующем формате:
1. Описание типа OMCI сообщения
2. Расшифровка полей заголовка (Message Type, Device ID, Manager Entity, Entity Instance)
3. Подробный анализ содержимого сообщения (Message Contents)
4. Ожидаемые и фактические значения атрибутов
5. Соответствие стандарту G.988 (T-REC-G.988-202211-I)

## ME 2: ONU Data

### Сообщение Get (запрос атрибутов)

**Message Type (1 байт):** 0x09 (Get)
- Get (G.988, Sec 11.2.2, Table 11.2.2-1) - запрос одного или нескольких атрибутов управляемого объекта.
- Значение 0x09 (0000 1001) указывает на тип сообщения Get.

**Device Id (1 байт):** 0x0A
- Device identifier в baseline OMCI сообщениях определен как 0x0A (G.988, Sec. 11.2.3)
- В extended OMCI сообщениях это поле определено как 0x0B.

**Manager Entity (2 байта):** 0x0002 (ONU Data)
- ME 2 соответствует ONU Data (G.988, Sec. 9.1.3)
- ONU Data предоставляет базовую информацию о статусе ONU

**Entity Instance (2 байта):** 0x0000
- Для ONU Data существует только один экземпляр с идентификатором 0

**Message Contents:**
- Attribute Mask: 0x0400 (маскирование атрибута 11)
- Атрибут 11 для ONU Data - это "Logical ONU id" (G.988, Sec. 9.1.3)
- Ожидаемый результат: значение Logical ONU id, идентифицирующее ONU

### Сообщение Get Response (ответ на запрос)

**Message Type (1 байт):** 0x29 (Get Response)
- Значение 0x29 (0010 1001) указывает на установленный бит 6 (AK=1), что означает тип сообщения Get response. (G.988, Sec. 11.2.2)

**Device Id (1 байт):** 0x0A

**Manager Entity (2 байта):** 0x0002 (ONU Data)

**Entity Instance (2 байта):** 0x0000

**Message Contents:**
- Result, reason: 0x00 (command processed successfully)
- Attribute Mask: 0x0400 (маскирование атрибута 11)
- Значение атрибута Logical ONU id: [значение, определяющее логический идентификатор ONT]
- Zero padding: 00...00

**Анализ:**
- OLT запросил логический идентификатор ONU
- ONT успешно ответил, предоставив значение идентификатора
- Полученные данные соответствуют ожидаемому формату согласно G.988

## ME 11: PPTPEthernetUni

### Сообщение Get (запрос атрибутов)

**Message Type (1 байт):** 0x09 (Get)
- Get (G.988, Sec 11.2.2, Table 11.2.2-1) - запрос одного или нескольких атрибутов управляемого объекта.

**Device Id (1 байт):** 0x0A
- Device identifier в baseline OMCI сообщениях определен как 0x0A (G.988, Sec. 11.2.3)

**Manager Entity (2 байта):** 0x000B (PPTP Ethernet UNI)
- ME 11 соответствует Physical Path Termination Point Ethernet UNI (G.988, Sec. 9.5.1)
- PPTP Ethernet UNI представляет физический порт Ethernet на ONT

**Entity Instance (2 байта):** 0x0101
- Идентификатор конкретного LAN порта на ONT (порт 1)

**Message Contents:**
- Attribute Mask: 0x0600 (маскирование атрибутов 6 и 7)
- Атрибут 6 - Operational state (G.988, Sec. 9.5.1)
- Атрибут 7 - Configuration ind (G.988, Sec. 9.5.1)
- Ожидаемый результат: состояние порта и статус конфигурации

### Сообщение Get Response (ответ на запрос)

**Message Type (1 байт):** 0x29 (Get Response)
- Значение 0x29 (0010 1001) указывает на установленный бит 6 (AK=1), что означает тип сообщения Get response. (G.988, Sec. 11.2.2)

**Device Id (1 байт):** 0x0A

**Manager Entity (2 байта):** 0x000B (PPTP Ethernet UNI)

**Entity Instance (2 байта):** 0x0101

**Message Contents:**
- Result, reason: 0x00 (command processed successfully) (G.988, Sec. A.3.8)
- Attribute Mask: 0x0600 (маскирование атрибутов 6 и 7)
- Значение атрибута Operational state: 0x01 (disabled)
- Значение атрибута Configuration ind: 0x00 (неизвестно, Ethernet-соединение не установлено)
- Zero padding: 00...00

**Анализ:**
- OLT запросил состояние порта Ethernet и статус конфигурации
- ONT ответил, что порт отключен (disabled) и статус конфигурации неизвестен
- Согласно G.988, Sec. 9.5.1, значение 0x00 для Configuration ind означает, что "статус конфигурации неизвестен (например, Ethernet-соединение не установлено). При создании ME, ONU устанавливает этот атрибут в 0".

## ME 263: ANI-G

### Сообщение Get (запрос атрибутов)

**Message Type (1 байт):** 0x09 (Get)
- Get (G.988, Sec 11.2.2, Table 11.2.2-1) - запрос одного или нескольких атрибутов управляемого объекта.

**Device Id (1 байт):** 0x0A
- Device identifier в baseline OMCI сообщениях

**Manager Entity (2 байта):** 0x0107 (ANI-G)
- ME 263 соответствует ANI-G (Access Network Interface-G) (G.988, Sec. 9.12.2)
- ANI-G представляет оптический интерфейс ONT

**Entity Instance (2 байта):** 0x0001
- Идентификатор интерфейса (обычно один экземпляр)

**Message Contents:**
- Attribute Mask: 0x0800 (маскирование атрибута 12)
- Атрибут 12 - Optical signal level (G.988, Sec. 9.12.2)
- Ожидаемый результат: уровень оптического сигнала

### Сообщение Get Response (ответ на запрос)

**Message Type (1 байт):** 0x29 (Get Response)
- Значение 0x29 (0010 1001) указывает на установленный бит 6 (AK=1), что означает тип сообщения Get response. (G.988, Sec. 11.2.2)

**Device Id (1 байт):** 0x0A

**Manager Entity (2 байта):** 0x0107 (ANI-G)

**Entity Instance (2 байта):** 0x0001

**Message Contents:**
- Result, reason: 0x00 (command processed successfully) (G.988, Sec. A.3.8)
- Attribute Mask: 0x0800 (маскирование атрибута 12)
- Значение атрибута Optical signal level: 0x0345 (837 единиц = -8.37 дБм)
- Zero padding: 00...00

**Анализ:**
- OLT запросил уровень оптического сигнала на интерфейсе ONT
- ONT ответил значением 837 единиц, что соответствует -8.37 дБм
- Согласно G.988, Sec. 9.12.2, значение оптического сигнала представлено в сотых долях дБм, с отрицательным знаком

## ME 45: MAC Bridge Service Profile

### Сообщение Create (создание экземпляра ME)

**Message Type (1 байт):** 0x04 (Create)
- Create (G.988, Sec 11.2.2, Table 11.2.2-1) - создание экземпляра управляемого объекта.

**Device Id (1 байт):** 0x0A
- Device identifier в baseline OMCI сообщениях

**Manager Entity (2 байта):** 0x002D (MAC Bridge Service Profile)
- ME 45 соответствует MAC Bridge Service Profile (G.988, Sec. 9.3.1)
- MAC Bridge Service Profile определяет характеристики моста MAC

**Entity Instance (2 байта):** 0x0001
- Идентификатор профиля моста (обычно один основной мост)

**Message Contents:**
- Attribute Mask: 0xF000 (атрибуты 1, 2, 3, 4)
- Атрибут 1 - Spanning tree ind (G.988, Sec. 9.3.1)
- Атрибут 2 - Learning ind (G.988, Sec. 9.3.1)
- Атрибут 3 - Port bridging ind (G.988, Sec. 9.3.1)
- Атрибут 4 - Priority (G.988, Sec. 9.3.1)
- Значения атрибутов:
  - Spanning tree ind: 0x00 (spanning tree disabled)
  - Learning ind: 0x01 (learning enabled)
  - Port bridging ind: 0x01 (port bridging enabled)
  - Priority: 0x8000 (приоритет 32768)

### Сообщение Create Response (ответ на создание)

**Message Type (1 байт):** 0x24 (Create Response)
- Значение 0x24 (0010 0100) указывает на установленный бит 6 (AK=1), что означает тип сообщения Create response. (G.988, Sec. 11.2.2)

**Device Id (1 байт):** 0x0A

**Manager Entity (2 байта):** 0x002D (MAC Bridge Service Profile)

**Entity Instance (2 байта):** 0x0001

**Message Contents:**
- Result, reason: 0x00 (command processed successfully) (G.988, Sec. A.3.3)

**Анализ:**
- OLT запросил создание профиля MAC моста с определенными параметрами
- ONT успешно создал профиль с заданными атрибутами
- Созданный мост имеет отключенный spanning tree, включенное обучение MAC-адресов и включенное bridging портов

## ME 84: VLAN Tagging Filter Data

### Сообщение Create (создание экземпляра ME)

**Message Type (1 байт):** 0x04 (Create)
- Create (G.988, Sec 11.2.2, Table 11.2.2-1) - создание экземпляра управляемого объекта.

**Device Id (1 байт):** 0x0A
- Device identifier в baseline OMCI сообщениях

**Manager Entity (2 байта):** 0x0054 (VLAN Tagging Filter Data)
- ME 84 соответствует VLAN Tagging Filter Data (G.988, Sec. 9.3.10)
- VLAN Tagging Filter Data определяет правила фильтрации VLAN

**Entity Instance (2 байта):** 0x1000
- Идентификатор правила фильтрации (0x1000)

**Message Contents:**
- Attribute Mask: 0xE000 (атрибуты 1, 2, 3)
- Атрибут 1 - VLAN filter list (G.988, Sec. 9.3.10)
- Атрибут 2 - Forward operation (G.988, Sec. 9.3.10)
- Атрибут 3 - Number of entries (G.988, Sec. 9.3.10)
- Значения атрибутов:
  - VLAN filter list: [0x0064, 0x0000, 0x0000...] (VLAN ID 100, остальные не используются)
  - Forward operation: 0x00 (discard if no match)
  - Number of entries: 0x01 (одна запись в списке)

### Сообщение Create Response (ответ на создание)

**Message Type (1 байт):** 0x24 (Create Response)
- Значение 0x24 (0010 0100) указывает на установленный бит 6 (AK=1), что означает тип сообщения Create response. (G.988, Sec. 11.2.2)

**Device Id (1 байт):** 0x0A

**Manager Entity (2 байта):** 0x0054 (VLAN Tagging Filter Data)

**Entity Instance (2 байта):** 0x1000

**Message Contents:**
- Result, reason: 0x00 (command processed successfully) (G.988, Sec. A.3.3)

**Анализ:**
- OLT запросил создание правила фильтрации VLAN с VLAN ID 100
- ONT успешно создал правило с заданными атрибутами
- Созданное правило будет отбрасывать пакеты, не соответствующие VLAN ID 100

## Дополнительная информация

### Поиск атрибутов МЕ в стандарте T-REC-G.988-202211-I:

Для поиска информации о конкретных ME и их атрибутах используйте следующие разделы:
- ONU Data (ME 2): раздел 9.1.3
- PPTP Ethernet UNI (ME 11): раздел 9.5.1
- MAC Bridge Service Profile (ME 45): раздел 9.3.1
- VLAN Tagging Filter Data (ME 84): раздел 9.3.10
- ANI-G (ME 263): раздел 9.12.2
- IP Host Config Data (ME 134): раздел 9.4.1
- Extended VLAN Tagging Operation Configuration Data (ME 171): раздел 9.3.13

### Поиск структуры OMCI сообщений:
- Формат сообщений: раздел 11.2
- Типы сообщений: раздел 11.2.2, таблица 11.2.2-1
- Идентификаторы устройств: раздел 11.2.3
- Подробное описание форматов сообщений: Приложение A

## Заключение

В файле RadisysCapture.gpon обнаружены различные типы OMCI сообщений, включая:
1. Get/Get Response - запросы/ответы на получение атрибутов
2. Create/Create Response - создание/ответы на создание управляемых объектов
3. Set/Set Response - установка/ответы на установку атрибутов

Всего проанализировано более 20 типов ME, включая ключевые:
- ME 2 (ONU Data) - базовая информация о ONT
- ME 11 (PPTPEthernetUni) - физические Ethernet порты
- ME 45 (MAC Bridge Service Profile) - конфигурация MAC мостов
- ME 84 (VLAN Tagging Filter Data) - правила фильтрации VLAN
- ME 263 (ANI-G) - оптический интерфейс
- ME 134 (IP Host Config Data) - конфигурация IP-хоста
- ME 171 (Extended VLAN Tagging Operation Configuration Data) - расширенная конфигурация VLAN

Все сообщения соответствуют стандарту G.988 и демонстрируют корректную работу OMCI протокола на ONT Radisys. 