Индексы. Васёв А.В.

## Задание 1
### Напишите запрос к учебной базе данных, который вернёт процентное отношение общего размера всех индексов к общему размеру всех таблиц.

```java
SELECT table_schema "sakila", sum(index_length)/sum(data_length + index_length)*100 "% соотношение" FROM information_schema.TABLES WHERE table_schema = "sakila";
```
![alt text](https://github.com/rus42/INDEXES/blob/main/Task_1.png)


## Задание 2
### Выполните explain analyze следующего запроса:

```java
select distinct concat(c.last_name, ' ', c.first_name), sum(p.amount) over (partition by c.customer_id, f.title)
from payment p, rental r, customer c, inventory i, film f
where date(p.payment_date) = '2005-07-30' and p.payment_date = r.rental_date and r.customer_id = c.customer_id and i.inventory_id = r.inventory_id
```

![alt text](https://github.com/rus42/INDEXES/blob/main/Task_2.1.png)

![alt text](https://github.com/rus42/INDEXES/blob/main/Task_2.2.png)

Самого быстрого выполнения запроса удалось добиться за счет добавления индексов и отказа от оператора OVER, в данном случае смысл его наличия теряется, так как ранее уже используется оператор DISTINCT. 
Скорость выполнения запроса была сокращена с более 18 сек до чуть менее 2 сек.

```java
CREATE INDEX customer_lastfirst_IDX USING BTREE ON sakila.customer (last_name, first_name);
CREATE INDEX payment_payment_date_IDX USING BTREE ON sakila.payment (payment_date); 
```
понятно, что по данному полю строить индекс не оптимально, но в данном случае это позволило в плане запроса EXPLAIN избежать сканирование всей таблицы payment.

```java
select distinct concat(c.last_name, ' ', c.first_name), sum(p.amount)
from payment p, rental r, customer c, inventory i, film f
where date(p.payment_date) = '2005-07-30' and p.payment_date = r.rental_date and r.customer_id = c.customer_id and i.inventory_id = r.inventory_id 
GROUP BY c.customer_id
```

![alt text](https://github.com/rus42/INDEXES/blob/main/Task_2.3.png)

![alt text](https://github.com/rus42/INDEXES/blob/main/Task_2.4.png)

## Задание 3
### Самостоятельно изучите, какие типы индексов используются в PostgreSQL. Перечислите те индексы, которые используются в PostgreSQL, а в MySQL — нет.

Типы индексов PostgreSQL:
1. B-Tree index 	
2. Поддерживаемые пространственные индексы(Spatial indexes) 	
3. Hash index
4. Bitmap index (отсутсвтует в MYSQL)
5. Inverted index
6. Partial index (отсутсвтует в MYSQL)
7. Function based index (отсутсвтует в MYSQL)








