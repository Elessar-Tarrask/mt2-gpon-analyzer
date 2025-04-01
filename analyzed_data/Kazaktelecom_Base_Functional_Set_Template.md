# 1.0.0 Базовый функциональный набор OMCI для Kazaktelecom

## 1. Общая информация:
- Версия: 1.0.0
- Дата: 31.03.2024
- Применение: ONT и OLT
- Стандарт: G.988

## 2. Обязательные Managed Entities (ME):

### a) Базовые ME (G.988 Clause 9.1):
- ME 256 (ONU-G) - Обязательный
- ME 257 (ONU2-G) - Обязательный
- ME 2 (ONU data) - Обязательный
- ME 7 (Software image) - Обязательный
- ME 5 (Cardholder) - Обязательный
- ME 6 (Circuit pack) - Обязательный
- ME 456 (ONU manufacturing data) - Обязательный

### b) Физический уровень (G.988 Clause 9.2):
- ME 263 (ANI-G) - Обязательный
- ME 262 (T-CONT) - Обязательный
- ME 268 (GEM port network CTP) - Обязательный
- ME 266 (GEM interworking termination point) - Обязательный
- ME 272 (GAL Ethernet profile) - Обязательный
- ME 277 (Priority queue) - Обязательный
- ME 280 (Traffic descriptor) - Обязательный

### c) Мостовая конфигурация (G.988 Clause 9.3):
- ME 45 (MAC bridge service profile) - Обязательный
- ME 47 (MAC bridge port configuration data) - Обязательный
- ME 130 (IEEE 802.1p mapper service profile) - Обязательный
- ME 84 (VLAN tagging filter data) - Обязательный
- ME 171 (Extended VLAN tagging operation configuration data) - Обязательный

### d) Физические интерфейсы (G.988 Clause 9.5):
- ME 11 (PPTP Ethernet UNI) - Обязательный

## 3. Дополнительные ME на основе анализа тестов:

### a) Мониторинг и диагностика:
- ME 425 (Ethernet Frame Extended PM 64 Bits) - Рекомендуется
- ME 341 (GEM port network CTP performance monitoring) - Рекомендуется

### b) Сервисная конфигурация:
- ME 329 (VEIP) - Обязательный для интернет-сервиса
- ME 134 (IP host config data) - Обязательный для IP-конфигурации

## 4. Требования к реализации:

### a) Базовые операции:
- Get All Alarms (ME 2)
- Test (ME 263)
- Test Response (ME 263)
- Test Result (ME 263)

### b) PLOAM сообщения:
- Upstream_Overhead
- Extended_Burst_Length
- Serial_Number_Request
- REI (Remote Error Indicator)

## 5. Особенности реализации:

### a) Обязательные функции:
- Полная поддержка VLAN (ME 84, ME 171)
- Управление трафиком (ME 262, ME 277)
- Мониторинг производительности
- Диагностика физического уровня

### b) Рекомендуемые функции:
- Расширенный мониторинг
- Детальная диагностика
- Оптимизация производительности

## 6. Критерии соответствия:
- Все обязательные ME должны быть реализованы
- Поддержка всех базовых операций
- Корректная обработка PLOAM сообщений
- Полная поддержка VLAN функциональности
- Реализация механизмов мониторинга

## 7. Примечания:
- ME 329 (VEIP) является обязательным для интернет-сервиса
- Все ME должны соответствовать версии G.988
- Должна быть обеспечена обратная совместимость
- Необходима поддержка всех базовых операций OMCI 