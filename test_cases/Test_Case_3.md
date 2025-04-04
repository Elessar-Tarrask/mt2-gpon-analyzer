# 1.0.0 Сравнение базовых операций ONU в разных capture файлах

**ID теста**: TC-BASIC-OPS-001  
**Дата**: 31.03.2024

## Цель
Сравнить и проанализировать базовые операции ONU, присутствующие в файлах RadisysCapture.gpon и CalixCapture.gpon.

## Конфигурация теста
- Файл RadisysCapture.gpon (Radisys ONT)
- Файл CalixCapture.gpon (Calix ONT)
- Анализатор GPON

## Процедура тестирования
1. Анализ PLOAM сообщений
2. Анализ OMCI сообщений
3. Сравнение операций между файлами

## Результаты тестирования

### 1. Общие операции в обоих файлах:
#### a) PLOAM сообщения:
- Upstream_Overhead
- Extended_Burst_Length
- Serial_Number_Request
- REI (только в CalixCapture)
   
#### b) OMCI сообщения:
- Get All Alarms (ONU Data)
- Get All Alarms Next (ONU Data)
- Test (ANI G)
- Test Response (ANI G)
- Test Result (ANI G)

### 2. Операции, присутствующие только в RadisysCapture.gpon (Radisys ONT):
#### a) OMCI операции:
- Set/Delete/Create для VEIP (ME 329)
- Set/Delete/Create для VLAN Tagging Filter Data
- Set/Delete/Create для MAC Bridge Service Profile
- Set/Delete/Create для GEM Interworking Termination Point
- Set/Delete/Create для T CONT
- Set/Delete/Create для Priority Queue
- Set/Delete/Create для Ethernet Frame Extended PM
- Set/Delete/Create для Pbit Mapper Service Profile
- Attribute Value Change для IP host config data

### 3. Операции, присутствующие только в CalixCapture.gpon (Calix ONT):
- PLOAM REI сообщения
- Более частые PLOAM Upstream_Overhead сообщения

## Критерии прохождения
1. Все базовые PLOAM операции должны быть корректно идентифицированы
2. Все базовые OMCI операции должны быть корректно идентифицированы
3. Различия между файлами должны быть четко определены
4. Каждая операция должна быть правильно классифицирована

## Заключение
Тест пройден успешно. Анализ показал:
- CalixCapture.gpon (Calix ONT) содержит базовые PLOAM и OMCI операции, необходимые для начальной работы ONU
- RadisysCapture.gpon (Radisys ONT) содержит расширенный набор OMCI операций, включая конфигурацию сервисов
- Различия в операциях обусловлены:
  * Разными фазами работы ONU в каждом capture файле
  * Различиями в реализации базовых операций между производителями ONU (Radisys и Calix)
  * Разными подходами к конфигурации сервисов у разных производителей 