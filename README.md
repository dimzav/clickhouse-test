## Функционал аналитики посещаемости сайта.

1) Создание таблицы посещений:

    ```clickhouse
    CREATE TABLE visits (
           `ip_address` UInt32,
           `country` String,
           `url` String,
           `visit_at` Date DEFAULT now()
       ) ENGINE = MergeTree(visit_at, (ip_address), 8192)
    ```

2) При входе пользователя на сайт необходимо собрать логи данных и агрегировать в промежуточное хранилище (например Redis), затем собрать данные в пакеты и записывать пакетами в БД.

3) Запрос на получение статистики по посещаемости сайта по дням за последний месяц в разрезе стран, из которых пользователи заходили на сайт:
    ```clickhouse
    SELECT country, visit_at,  count(*) AS visits_count FROM visits WHERE toYYYYMM(visit_at) = toYYYYMM(now()) GROUP BY country, visit_at ORDER BY country ASC, visit_at ASC
    ```
4) Запрос на удаление неактуальных данных, более одного года, по пользователям:
    ```clickhouse
   ALTER TABLE `visits` DELETE WHERE visit_at < toDate(subtractYears(now(), 1))
    ```
    
5) Для теста заполнить тестовыми данными из файла `data.csv`
