# Работа в PostgreSQL. Задачи были взяты из проекта Яндекс.


## Задача № 1: 
Выгрузи из таблицы invoice адреса из поля billing_address и страну из поля billing_country. Отфильтруй данные по стране: оставьте только США (англ. USA), Индию (англ. India), Канаду (англ. Canada), Аргентину (англ. Argentina) и Францию (англ. France).

##Решение:

SELECT billing_address,
       billing_country
FROM invoice
WHERE billing_country IN ('USA',
                         'India',
                         'Canada',
                         'Argentina',
                         'France');


## Задача № 2: 

Выгрузи все поля из таблицы invoice. Информацию о дате покупки хранит поле invoice_date: выбери только те заказы, которые были оформлены в период с '2009-03-04' по '2012-02-09' включительно. Обрати внимание, что у поля invoice_date тип данных varchar.
Сумма покупки total должна быть меньше 5. Кроме того, исключите из запроса Канаду (англ. Canada), Бразилию (англ. Brazil) и Финляндию (англ. Finland). Название страны содержит поле billing_country.

##Решение: 

SELECT *
FROM invoice
WHERE CAST(invoice_date AS DATE) BETWEEN '2009-03-04' AND '2012-02-09'
AND total < 5
AND billing_country NOT IN ('Canada',
                       'Brazil',
                       'Finland');


## Задача № 3:
Выдели категории в таблице staff, которая хранит информацию о сотрудниках. Выведи на экран поля last_name, first_name и title. Категории нужно выделить на основе значений в поле title — оно содержит информацию о должности сотрудника:
Если в title встречается слово 'IT', в новом поле будет отображена категория 'разработка'.
Если в title встречается слово 'Manager' и нет слова 'IT', в новом поле отобразится категория 'отдел продаж'.
Если в title встречается слово 'Support', в новом поле появится категория 'поддержка'.

##Решение: 

SELECT last_name,
       first_name,
       title, 
      CASE
          WHEN title LIKE '%IT%' THEN 'разработка'
          WHEN title LIKE '%Manager%' AND title NOT LIKE '%IT%' THEN 'отдел продаж'
          WHEN title LIKE '%Support%' THEN 'поддержка'
      END
FROM staff;

##Задача № 4:
Посчитай, сколько пользователей с почтовыми доменами yahoo и gmail обслужил каждый сотрудник. В итоговой таблице должны быть два поля:
идентификатор сотрудника,
количество пользователей.

##Решение:

SELECT support_rep_id, 
COUNT (customer_id) 
FROM client 
WHERE email LIKE '%yahoo%' OR email LIKE '%gmail%' 
GROUP BY support_rep_id;


##Задача № 5:
Сравни общую сумму выручки (поле total) за каждый день этого месяца: выведи день в формате '2011-09-01' и сумму. Информацию о дате заказа хранит поле invoice_date. Не забудь изменить тип данных в этом поле, чтобы использовать операторы для работы с датой.

##Решение: 

SELECT SUM(total),
       CAST(invoice_date AS DATE)
FROM invoice
GROUP BY invoice_date
HAVING EXTRACT(YEAR FROM CAST(invoice_date AS TIMESTAMP)) IN (2011)
        AND EXTRACT(MONTH FROM CAST(invoice_date AS TIMESTAMP)) IN (9)
        AND SUM(total) > 1
        AND SUM(total) < 10;


##Задача № 6:
Нужно объединить данные двух таблиц: track и invoice_line. Таблица track хранит информацию о музыкальных треках в магазине, названия треков указаны в поле name. Таблица invoice_line содержит данные о купленных треках, их стоимость указана в поле unit_price. В обеих таблицах есть поле track_id — в нём содержатся идентификаторы музыкальных треков.
Выгрузи таблицу, в которой названию трека будет соответствовать его стоимость. Отбери все уникальные записи. Если какой-либо из треков не покупали или у купленного трека нет названия — такие записи не должны войти в таблицу. Оставь в итоговой таблице первые 20 записей.

##Решение:

SELECT DISTINCT
       t.name,
	   i.unit_price
FROM track AS t
INNER JOIN invoice_line AS i ON t.track_id = i.track_id
WHERE t.name IS NOT NULL
LIMIT 20;

##Задача № 7:
Идентификатор плейлиста теперь указан в итоговой таблице. Но что это за плейлисты — непонятно. Эту информацию можно взять в четвёртой таблице — playlist. Таблица содержит поле playlist_id с идентификатором плейлиста и поле name — с его названием. Добавь в итоговую таблицу поле name. Условия те же: данные без совпадения не должны попасть в таблицу. Ограничь вывод первыми 20 записями.

##Решение:

SELECT t.name,
       i.unit_price,
       pt.playlist_id,
       p.name-- укажи необходимое поле
FROM track AS t
INNER JOIN invoice_line AS i ON t.track_id=i.track_id
INNER JOIN playlist_track AS pt ON t.track_id=pt.track_id
INNER JOIN playlist AS p ON pt.playlist_id = p.playlist_id
WHERE t.name IS NOT NULL
LIMIT 20;





