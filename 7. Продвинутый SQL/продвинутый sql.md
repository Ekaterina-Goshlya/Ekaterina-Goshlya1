# Анализ сервиса вопросов и ответов о программировании.

## Описание проекта 
Мы будем работать с базой данных StackOverflow — сервиса вопросов и ответов о программировании. StackOverflow похож на социальную сеть — пользователи сервиса задают вопросы, отвечают на посты, оставляют комментарии и ставят оценки другим ответам.
Мы будем работать с версией базы, где хранятся данные о постах за 2008 год, но в таблицах имеется информация и о более поздних оценках, которые эти посты получили. 
С помощью SQL посчитать ключевые метрики сервис-системы вопросов и ответов о программировании.

![Image (4)](https://pictures.s3.yandex.net/resources/Frame_353_1_1664969703.png)

Таблицы:
- `badges` - хранит информацию о значках, которые присуждаются за разные достижения. 
- `post_types` - содержит информацию о типе постов.
- `posts` - содержит информацию о постах.
- `users` - содержит информацию о пользователях.
- `vote_types` - содержит информацию о типах голосов.
- `votes` - содержит информацию о голосах за посты.  

1.
Выведите общую сумму просмотров у постов, опубликованных в каждый месяц 2008 года. Если данных за какой-либо месяц в базе нет, такой месяц можно пропустить. Результат отсортируйте по убыванию общего количества просмотров.

```
SELECT CAST(DATE_TRUNC('month', creation_date) AS date) AS month, SUM(views_count) AS sum
FROM stackoverflow.posts
WHERE creation_date::date BETWEEN '2008-01-01' AND '2008-12-31'
GROUP BY CAST(DATE_TRUNC('month', creation_date) AS date)
ORDER BY sum DESC
```
2.
Выведите имена самых активных пользователей, которые в первый месяц после регистрации (включая день регистрации) дали больше 100 ответов. Вопросы, которые задавали пользователи, не учитывайте. Для каждого имени пользователя выведите количество уникальных значений user_id. Отсортируйте результат по полю с именами в лексикографическом порядке.

```
SELECT display_name,
       COUNT(DISTINCT(user_id))
FROM stackoverflow.users AS u JOIN stackoverflow.posts AS p ON u.id=p.user_id
JOIN stackoverflow.post_types AS t ON p.post_type_id=t.id
WHERE (DATE_TRUNC('day', p.creation_date) <= DATE_TRUNC('day', u.creation_date) + INTERVAL '1 month') AND (p.post_type_id=2)
GROUP BY display_name
HAVING COUNT(p.id) > 100
```
3.
Выведите количество постов за 2008 год по месяцам. Отберите посты от пользователей, которые зарегистрировались в сентябре 2008 года и сделали хотя бы один пост в декабре того же года. Отсортируйте таблицу по значению месяца по убыванию.

```
WITH users AS (SELECT u.id
               FROM stackoverflow.posts AS p
               JOIN stackoverflow.users AS u ON p.user_id=u.id
               WHERE DATE_TRUNC('month', u.creation_date)::date = '2008-09-01' 
                   AND DATE_TRUNC('month', p.creation_date)::date = '2008-12-01'
               GROUP BY u.id
               HAVING COUNT(p.id) > 0)

SELECT COUNT(p.id),
       DATE_TRUNC('month', p.creation_date)::date
FROM stackoverflow.posts AS p
WHERE p.user_id IN (SELECT *
                    FROM users)
      AND DATE_TRUNC('year', p.creation_date)::date = '2008-01-01'
GROUP BY DATE_TRUNC('month', p.creation_date)::date
ORDER BY DATE_TRUNC('month', p.creation_date)::date DESC;
```
4.
Используя данные о постах, выведите несколько полей:
- идентификатор пользователя, который написал пост;
- дата создания поста;
- количество просмотров у текущего поста;
- сумма просмотров постов автора с накоплением.
Данные в таблице должны быть отсортированы по возрастанию идентификаторов пользователей, а данные об одном и том же пользователе — по возрастанию даты создания поста.

```
SELECT user_id, creation_date, views_count,
SUM(views_count) OVER (PARTITION BY user_id ORDER BY creation_date)
FROM stackoverflow.posts 
ORDER BY user_id, creation_date
```
5.
Сколько в среднем дней в период с 1 по 7 декабря 2008 года включительно пользователи взаимодействовали с платформой? Для каждого пользователя отберите дни, в которые он или она опубликовали хотя бы один пост. Нужно получить одно целое число — не забудьте округлить результат.

```
WITH users AS (SELECT p.user_id, 
      COUNT(distinct p.creation_date::date)
FROM stackoverflow.posts AS p
WHERE CAST(creation_date AS date) BETWEEN '2008-12-1' AND '2008-12-7' 
GROUP BY p.user_id
HAVING COUNT(p.id)>=1)
SELECT ROUND(AVG(count))
FROM users
```
6.
На сколько процентов менялось количество постов ежемесячно с 1 сентября по 31 декабря 2008 года? Отобразите таблицу со следующими полями:
- Номер месяца.
- Количество постов за месяц.
- Процент, который показывает, насколько изменилось количество постов в текущем месяце по сравнению с предыдущим.
Если постов стало меньше, значение процента должно быть отрицательным, если больше — положительным. Округлите значение процента до двух знаков после запятой.
Напомним, что при делении одного целого числа на другое в PostgreSQL в результате получится целое число, округлённое до ближайшего целого вниз. Чтобы этого избежать, переведите делимое в тип numeric.

```
with a AS (SELECT EXTRACT(month from creation_date) AS num, COUNT(id) AS cnt
           FROM stackoverflow.posts
           WHERE  creation_date::date BETWEEN '2008-09-01' AND '2008-12-31'
          GROUP BY 1)

          SELECT num, cnt, ROUND(((cnt::numeric/LAG(cnt) OVER (ORDER BY num))-1)*100,2)
          FROM a
```
7.
Найдите пользователя, который опубликовал больше всего постов за всё время с момента регистрации. Выведите данные его активности за октябрь 2008 года в таком виде:
- номер недели;
- дата и время последнего поста, опубликованного на этой неделе.

```
SELECT
DISTINCT(EXTRACT(week FROM creation_date::date)),
MAX(creation_date) OVER (ORDER BY EXTRACT(week FROM creation_date::date))
FROM stackoverflow.posts
WHERE user_id = 22656
AND creation_date::date BETWEEN '2008-10-01' AND '2008-10-31'
```
