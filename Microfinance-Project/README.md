## Проект «Дашборд для микрофинансовой организации»

### Процесс загрузки и обработки данных в Power Query
Обе таблицы crm_mart, mfo_contracts были загружены в Power Qeury через подключению к источнику БД Postgres, причем таблица crm_mart с использованием SQL запроса, т.к. содержала в себе одну колонку в json-формате, которую нужно было распарсить:
https://github.com/adilbek1984/Power-BI-Projects/tree/main/Microfinance-Project/images/NumOfContracts.png
