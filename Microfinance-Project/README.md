## Проект «Дашборд для микрофинансовой организации»

### Процесс загрузки и обработки данных в Power Query
Обе таблицы crm_mart, mfo_contracts были загружены в Power Qeury через подключению к источнику БД Postgres, причем таблица crm_mart с использованием SQL запроса, т.к. содержала в себе одну колонку в json-формате, которую нужно было распарсить:

![Image](https://github.com/adilbek1984/Power-BI-Projects/blob/main/Microfinance-Project/images/Postgres%20connection.png)

Далее на основе этих 2 таблиц были созданы 4 референсные таблицы: 
-	2 таблицы измерений: dimClients, dimCompanySize;
-	2 таблицы фактов: mfo_contracts, crm_mart.

![Image](https://github.com/adilbek1984/Power-BI-Projects/blob/main/Microfinance-Project/images/dimClients.png)

![Image](https://github.com/adilbek1984/Power-BI-Projects/blob/main/Microfinance-Project/images/dimCompanySize.png)

Таблицы измерений были созданы на основе таблиц crm_mart, mfo_contracts после чего поля, связанные с клиентами (region, location, business_type) и категориями компаний (krp_name) были удалены из таблиц фактов, что позволило сократить размер модели.

Далее по каждой из 4 таблиц были проведены работы по очистке данных: исключены дублирования по таблицам измерений, удалены пустые столбцы, столбцы приведены к соотетствующим типам данных (из текстового формата в числовой, из текстового формата к формату даты и т.д.). 

После всех процедур по очистке и преобразованию данных все референсные таблицы были загружены в модель данных, а «питающие» их исходные таблицы отключены от загрузки в модель.

![Image](https://github.com/adilbek1984/Power-BI-Projects/blob/main/Microfinance-Project/images/tables%20to%20model.png)

### Процесс работы с данными во вкладке Table view

*Виртуальная таблица Calendar*

После того как данные были загружены в модель нам необходимо создать таблицу с датами, чтобы можно было формировать графики в разрезе дат (год, месяц, квартал, день и т.д.). Для этого с использованием языка DAX создаем виртуальную таблицу Calendar на основе минимальной и максимальной даты по полю mfo_contracts[agreement_date]:

![Image](https://github.com/adilbek1984/Power-BI-Projects/blob/main/Microfinance-Project/images/Calendar%20virual%20table.png)

*Виртуальная таблица Clients_scoring*

В целях расчета уровня благонадежности клиентов и отнсения их к той или иной категории риска с использованием DAX создана виртуальная таблица Clients_scoring, в которой агрегированы самые актуальные данные из базы CRM компании (таблица crm_mart). Для определения актуальности («свежести данных») использовалась колонка crm_mart[season_id].

![Image](https://github.com/adilbek1984/Power-BI-Projects/blob/main/Microfinance-Project/images/ClientsScoring.png)

После чего в данную таблицу были добавлены виртуальные колонки: критерии, на основе которых расчитывается уровень благонадежности клиента, уровень благонадежности и группа риска, к которой относится компания:

![Image](https://github.com/adilbek1984/Power-BI-Projects/blob/main/Microfinance-Project/images/ArrestsAndConvictions%20criterion.png)
*Критерий «аресты, судимости, задолженности»*

![Image](https://github.com/adilbek1984/Power-BI-Projects/blob/main/Microfinance-Project/images/CompanySizeScore%20criterion.png)
*Критерий «размер предприятия»*

![Image](https://github.com/adilbek1984/Power-BI-Projects/blob/main/Microfinance-Project/images/Engagement%20criterion.png)
*Критерий «вовлеченность клиента в услуги холдинга»*

![Image](https://github.com/adilbek1984/Power-BI-Projects/blob/main/Microfinance-Project/images/Scoring.png)
*Уровень благонадежности*

![Image](https://github.com/adilbek1984/Power-BI-Projects/blob/main/Microfinance-Project/images/RiskGroup.png)
*Группа риска*

### Процесс работы с данными во вкладке Model view

После того, как наши таблицы dimClients, mfo_contracts, crm_mart, dimCompanySize были загружены в модель, созданы дополнительно 2 виртуальные таблицы (Calendar, ClientsScoring) необходимо приступить к формированию модели данных, т.е. формированию связей между таблицами фактов и таблицами измерений.

![Image](https://github.com/adilbek1984/Power-BI-Projects/blob/main/Microfinance-Project/images/MFO%20Data%20model.png) 

Связи в модели данных были построены по следующему принципу: связи один ко многим сформированы от таблиц измерений к таблицам фактов, связи один к одному – между таблицаи измерений. 

### Процесс работы с отчетом и визуализациями во вкладке Report view

Для начала необходимо определиться с основными KPI, а также с основными графиками, которые мы хотим отобразить в отчете Power BI.

**Основные KPI:**
1.	Количество уникальных клиентов;
2.	Количество договор на микрофинансирование;
3.	Объем выданных кредитов;
4.	Сумма оплаченных кредитов;
5.	Средняя месячная эффективная ставка вознаграждения по договорам микрофинансирования
6.	Средний срок просрочки по обязательствам

**Основные графики и визуализация:**

*Дашборд «Услуги МКО»*
1.	Количество заключенных договоров на микрофинсирование
2.	Объем выдачи кредитов по годам
3.	Объем оплаченных кредитов
4.	Объем выручки по годам
5.	Карта регионального распределения кредитов
6.	Динамика заключенных договоров по годам
7.	Доля договоров имеющих штрафы
8.	Средний объем займа на клиента по годам

*Дашборд «Клиенты МКО»*
1.	Динамика клиентов по годам
2.	Топ 10 клиентов по объему займов
3.	Распределение клиентов по уровню благонадежности
4.	Распределение клиенов по группам риска
5.	Распределение займов по клиентам в зависимости от вида экономической деятельности
6.	Распределение клиентов по категориям предприятий

*Дашборд «База CRM»*
1.	Доля контрактов с просроченными обязательствами
2.	Распрделение клиентов по группам риска
3.	Распределение клиентов по категориям предприятий
4.	Распределение клиентов по уровню благонадежности
5.	График корреляции суммы договора и максимальной просрочки
6.	Таблица распределения клиентов по группам риска и уровням благонадежности

Для создания визуализаций и графиков нам необходимо создать соответствующие меры с использованием DAX-формул:

1. Количество уникальных клиентов
   
![Image](https://github.com/adilbek1984/Power-BI-Projects/blob/main/Microfinance-Project/images/NumOfClients.png)

2.	Количество уникальных контрактов

![Image](https://github.com/adilbek1984/Power-BI-Projects/blob/main/Microfinance-Project/images/NumOfContracts.png)

3. Объем выданых займов

![Image](https://github.com/adilbek1984/Power-BI-Projects/blob/main/Microfinance-Project/images/TotalVolumeOfLoans.png)

4.	Объем оплаченных кредитов

![Image](https://github.com/adilbek1984/Power-BI-Projects/blob/main/Microfinance-Project/images/TotalPaidVolume.png)

5. Объем выручки

![Image](https://github.com/adilbek1984/Power-BI-Projects/blob/main/Microfinance-Project/images/Revenue.png)

6. Средний объем займа на одного клиента

![Image](https://github.com/adilbek1984/Power-BI-Projects/blob/main/Microfinance-Project/images/AvgLoanSize.png)

7. Количество уникальных клиентов в таблице crm_mart

![Image](https://github.com/adilbek1984/Power-BI-Projects/blob/main/Microfinance-Project/images/UniqueCRMClients.png)

8. Количество договоров в таблице crm_mart

![Image](https://github.com/adilbek1984/Power-BI-Projects/blob/main/Microfinance-Project/images/NumOfCRMContracts.png)

9. Объем продаж по товарам основного бизнеса в таблице crm_mart

![Image](https://github.com/adilbek1984/Power-BI-Projects/blob/main/Microfinance-Project/images/TotalVolumeOfContracts.png)

10. Количество договоров имеющих просрочку

![Image](https://github.com/adilbek1984/Power-BI-Projects/blob/main/Microfinance-Project/images/NoOverdueContracts.png)

11. Доля договоров имеющих просрочку по платежам

![Image](https://github.com/adilbek1984/Power-BI-Projects/blob/main/Microfinance-Project/images/OverdueShare.png)

12. 
