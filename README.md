# data-lab
# Этап 1. Запуск виртуальной машины и HDFS
Была использована виртуальная машина cloudera-quickstart-vm-5.4.2-0-virtualbox в среде VirtualBox.
После запуска машины был открыт терминал и выполнены команды для проверки работы HDFS
<img width="801" height="690" alt="1" src="https://github.com/user-attachments/assets/991f640c-ec97-43fb-a93d-5fab95dfe20d" />
<img width="804" height="692" alt="2" src="https://github.com/user-attachments/assets/be58591b-881c-43d1-ac75-c93d27a9e2ff" />
<img width="797" height="670" alt="3" src="https://github.com/user-attachments/assets/ea9075f3-0d0e-4ab1-bd3e-1b7b755e5237" />
# Этап 2. Создание базы данных в Hive
В терминале была запущена командная строка Hive:
Создана база данных для работы:
```sql
    CREATE DATABASE my_lab;
    USE my_lab;
```
<img width="659" height="466" alt="4" src="https://github.com/user-attachments/assets/7e4f3ec2-ba19-4261-ab8c-2a9c43993630" />

# Этап 3. Создание таблиц в Hive

<img width="805" height="734" alt="5" src="https://github.com/user-attachments/assets/73fd64dc-be32-4ee2-bfe8-1c277afa0272" />
Для реализации были созданы таблицы:
Справочники

```sql
CREATE TABLE dim_category (
category_id INT,
category_name STRING
);
CREATE TABLE dim_city (
city_id INT,
city_name STRING,
region STRING
);
```
<img width="853" height="247" alt="6" src="https://github.com/user-attachments/assets/53b7043f-a1b8-4e46-ad06-e03440dee1c8" />

<img width="877" height="337" alt="7" src="https://github.com/user-attachments/assets/114c08ee-da79-4fc3-a86e-541c86af5284" />
 
Нормализованные измерения
```sql
CREATE TABLE dim_product_snow (
product_id INT,
product_name STRING,
category_id INT,
price FLOAT
);
 CREATE TABLE dim_customer_snow (
customer_id INT,
name STRING,
city_id INT,
age INT
);
 CREATE TABLE dim_date_snow (
date_id INT,
full_date STRING,
year INT,
month INT,
day INT
);
 CREATE TABLE fact_sales_snow (
sale_id INT,
product_id INT,
customer_id INT,
date_id INT,
quantity INT,
amount FLOAT
);
```

<img width="867" height="335" alt="8" src="https://github.com/user-attachments/assets/7f2499de-1896-4276-b60e-7bf688904e8e" />
<img width="858" height="326" alt="9" src="https://github.com/user-attachments/assets/ee98d8d4-a6c5-4926-a263-d04a13e80d24" />
<img width="876" height="360" alt="10" src="https://github.com/user-attachments/assets/924272aa-6bd5-4521-8341-abebc92c89da" />
<img width="848" height="467" alt="11" src="https://github.com/user-attachments/assets/66bad50c-d308-47de-b162-a2ea5acc4cac" />


# Этап 4. Заполнение таблиц

```sql
INSERT INTO dim_category VALUES (1, 'Electronics'), (2, 'Books');

INSERT INTO dim_city VALUES
(1, 'Moscow', 'Central'),
(2, 'Saint Petersburg', 'North-West'),
(3, 'Kazan', 'Volga'),
(4, 'Novosibirsk', 'Siberia');

INSERT INTO dim_product_snow VALUES
(1, 'Laptop', 1, 55000),
(2, 'Smartphone', 1, 30000),
(3, 'Python Book', 2, 1200),
(4, 'SQL Book', 2, 1500),
(5, 'Headphones', 1, 3500);

INSERT INTO dim_customer_snow VALUES
(1, 'John Smith', 1, 28),
(2, 'Maria Ivanova', 2, 32),
(3, 'Jane Doe', 1, 45),
(4, 'Robert Johnson', 3, 23),
(5, 'Anna Williams', 4, 31);

INSERT INTO dim_date_snow VALUES
(20240301, '2024-03-01', 2024, 3, 1),
(20240302, '2024-03-02', 2024, 3, 2),
(20240303, '2024-03-03', 2024, 3, 3),
(20240304, '2024-03-04', 2024, 3, 4),
(20240305, '2024-03-05', 2024, 3, 5);

INSERT INTO fact_sales_snow VALUES
(1, 1, 1, 20240301, 1, 55000),
(2, 2, 2, 20240301, 2, 60000),
(3, 3, 1, 20240302, 3, 3600),
(4, 4, 3, 20240302, 1, 1500),
(5, 1, 4, 20240303, 1, 55000),
(6, 5, 2, 20240303, 2, 7000),
(7, 2, 5, 20240304, 1, 30000),
(8, 3, 3, 20240304, 2, 2400),
(9, 4, 1, 20240305, 1, 1500),
(10, 5, 4, 20240305, 1, 3500);
```
# Этап 5. Примеры аналитических запросов

## 5.1. Запрос к модели "Звезда"

•	Созданы таблицы: fact_sales_snow + dim_product_snow, dim_customer_snow, dim_date_snow, dim_category, dim_city

•	Через JOIN получается плоская витрина — звезда

<img width="739" height="612" alt="12" src="https://github.com/user-attachments/assets/289314ea-6292-4184-89b4-2370e1beb615" />

## 5.2. Запрос к модели "Снежинка"
•	Таблицы нормализованы: категории и города вынесены отдельно

•	Запрос с JOIN через category_id и city_id — снежинка

<img width="814" height="730" alt="13" src="https://github.com/user-attachments/assets/887d2d44-55b2-4ccd-8302-cd344aca4f78" />

## 5.3. Запрос к модели Data Vault

Для Data Vault нужно создать

•	Хабы: hub_customer, hub_product, hub_date, hub_city, hub_category

•	Сателлиты: sat_customer, sat_product, sat_city, sat_category

•	Линки: link_sales, link_product_category, link_customer_city

•	Все таблицы заполнены

<img width="769" height="623" alt="14" src="https://github.com/user-attachments/assets/a588117f-a552-46f9-94a5-51af35a0bdc7" />
<img width="1220" height="424" alt="15" src="https://github.com/user-attachments/assets/82371da8-6294-4609-905e-0b99c455ec1d" />

# Вывод

В ходе работы были:

•	запущены HDFS и Hive,

•	создана база данных,

•	реализованы три модели данных (Звезда, Снежинка, Data Vault),

•	выполнены запросы,

•	получены аналитические результаты.

Все скриншоты этапов прилагаются.
