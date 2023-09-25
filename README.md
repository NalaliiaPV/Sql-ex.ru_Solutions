# [Sql-ex.ru solutions](https://www.sql-ex.ru/learn_exercises.php)
My solution of exercises from the portal Sql-ex.ru (training stage)

### [Exercises №16](https://www.sql-ex.ru/learn_exercises.php?LN=16)
```
SELECT DISTINCT p1.model as model1, p2.model as model2, p1.speed, p1.ram   
FROM
    PC as p1
JOIN
    PC as p2 ON p1.speed = p2.speed
           AND p1.ram = p2.ram
           AND p1.model > p2.model
```

### [Exercises №17](https://www.sql-ex.ru/learn_exercises.php?LN=17)
```
SELECT DISTINCT p.type, l.model, l.speed
FROM
    Laptop as l
JOIN
    product as p on p.model = l.model 
WHERE
    speed < (
            select min(speed)
            from PC
            )
```

### [Exercises №18](https://www.sql-ex.ru/learn_exercises.php?LN=18)
```
SELECT DISTINCT
    Product.maker,
    Printer.price
FROM
    Product
JOIN
    Printer ON Product.model = Printer.model
WHERE
    Printer.color = 'y' AND
    Printer.price IN (
        SELECT MIN(price)
        FROM Printer
        WHERE color = 'y'
    )
```

### [Exercises №24](https://www.sql-ex.ru/learn_exercises.php?LN=24)
```
SELECT maker
FROM product 
WHERE model IN (
               SELECT DISTINCT model
               FROM PC
               WHERE speed >=750
              )
INTERSECT
SELECT maker
FROM product 
WHERE model IN (
               SELECT DISTINCT model
               FROM Laptop
               WHERE speed >=750)

```

### [Exercises №25](https://www.sql-ex.ru/learn_exercises.php?LN=25)
```
WITH PC_Makers as (
  SELECT a.maker, b.code, b.model, b.speed, b.ram, b.hd, b.cd, b.price
  FROM product as a
  JOIN PC as b ON a.model=b.model 
  WHERE a.type = 'PC' --приклеили производиля к PC 
  )

SELECT DISTINCT maker
FROM PC_Makers
WHERE ram IN (
               SELECT min (ram)
               FROM PC -- определили мин значение ram
               ) AND 
      speed IN (
               SELECT max(speed)
               FROM (
                     SELECT speed
                     FROM PC
                     WHERE ram IN (
                                   SELECT min (ram)
                                   FROM PC
                                   )
                     ) as t1 -- определили максимальное значение скорости = 500 
               ) AND
      maker IN (
                SELECT DISTINCT maker
                FROM product
                WHERE type = 'printer'
                )
```

### [Exercises №26](https://www.sql-ex.ru/learn_exercises.php?LN=26)
```
SELECT avg(price) avg_price
FROM (
    SELECT model, price from pc  
    union all
    SELECT model, price from Laptop  
    ) as etc
INNER JOIN Product ON etc.model = product.model
WHERE maker = 'A'  
```

### [Exercises №27](https://www.sql-ex.ru/learn_exercises.php?LN=27)
```
SELECT DISTINCT a.maker, avg(b.hd)
FROM product as a
JOIN PC as b ON b.model = a.model
WHERE maker IN (
        SELECT DISTINCT maker FROM product
        WHERE type = 'PC'
        INTERSECT 
        SELECT DISTINCT maker FROM product
        WHERE type = 'printer'
        ) AND --определили список производителей и моделей
     type = 'PC' --вычленили только ПК
GROUP BY a.maker
```

### [Exercises №29](https://www.sql-ex.ru/learn_exercises.php?LN=29)
```
WITH all_transactions as (
  SELECT DISTINCT point,date FROM Income_o
  UNION
  SELECT DISTINCT point,date FROM Outcome_o
  )

SELECT a.point, a.date, i.inc, o.out
FROM all_transactions as a
LEFT JOIN Income_o as i 
ON a.point=i.point AND a.date=i.date
LEFT JOIN Outcome_o as o
ON a.point=o.point AND a.date=o.date
```

### [Exercises №30](https://www.sql-ex.ru/learn_exercises.php?LN=30)
```
with all_transactions as (
  select distinct point,date from Income
  union
  select distinct point,date from Outcome --19 строк
  ),
Income_grouped as (
  select point, date, sum(inc) as sum_inc from Income
  group by point, date
  ),
Outcome_grouped as (
  select point, date, sum(out) as sum_out from Outcome
  group by point, date
  )

Select a.point, a.date, o.sum_out, i.sum_inc
from all_transactions as a
left join Income_grouped as i ON a.point=i.point AND a.date=i.date
left join Outcome_grouped as o ON a.point=o.point AND a.date=o.date
```

### [Exercises №35](https://www.sql-ex.ru/learn_exercises.php?LN=35)
```
SELECT model, type
FROM
    Product
WHERE
    model NOT LIKE '%[^0-9]%' OR
    model NOT LIKE '%[^A-Za-z]%'
```

### [Exercises №36](https://www.sql-ex.ru/learn_exercises.php?LN=36)
```
SELECT DISTINCT ship as name
FROM Outcomes 
WHERE ship IN ( select distinct class from classes) --нашли 2
UNION
SELECT DISTINCT name
FROM ships
WHERE name=class --нашли 7 головных
```

### [Exercises №37](https://www.sql-ex.ru/learn_exercises.php?LN=37)
```
with ships_all as (
    select distinct ship as name, ship as class
    from Outcomes 
    where ship IN (
                   select distinct class from classes)
    UNION
    select distinct name, class
    from ships
)

select class
from (
      select distinct name, class
      from ships_all
     ) as t1
group by class
having count(name) = 1

```
