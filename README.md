# [Sql-ex.ru solutions](https://www.sql-ex.ru/learn_exercises.php)
My solution of exercises from the portal Sql-ex.ru (training stage)

### [Exercise №16](https://www.sql-ex.ru/learn_exercises.php?LN=16)
```
SELECT DISTINCT p1.model as model1, p2.model as model2, p1.speed, p1.ram   
FROM
    PC as p1
JOIN
    PC as p2 ON p1.speed = p2.speed
           AND p1.ram = p2.ram
           AND p1.model > p2.model
```

### [Exercise №17](https://www.sql-ex.ru/learn_exercises.php?LN=17)
```
SELECT DISTINCT p.type, l.model, l.speed
FROM
    Laptop as l
JOIN
    product as p ON p.model = l.model 
WHERE
    speed < (
            SELECT min(speed)
            FROM PC
            )
```

### [Exercise №18](https://www.sql-ex.ru/learn_exercises.php?LN=18)
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

### [Exercise №23](https://www.sql-ex.ru/learn_exercises.php?LN=23)
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

### [Exercise №24](https://www.sql-ex.ru/learn_exercises.php?LN=24)
```
WITH all_products AS (
    SELECT model, price
    FROM PC
    UNION
    SELECT model, price
    FROM Laptop
    UNION
    SELECT model, price
    FROM Printer
)

SELECT model
FROM all_products
WHERE price = (
    SELECT MAX(price)
    FROM all_products
);

```

### [Exercise №25](https://www.sql-ex.ru/learn_exercises.php?LN=25)
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

### [Exercise №26](https://www.sql-ex.ru/learn_exercises.php?LN=26)
```
SELECT avg(price) avg_price
FROM (
    SELECT model, price FROM pc  
    UNION ALL
    SELECT model, price FROM Laptop  
    ) as etc
INNER JOIN Product ON etc.model = product.model
WHERE maker = 'A'  
```

### [Exercise №27](https://www.sql-ex.ru/learn_exercises.php?LN=27)
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

### [Exercise №29](https://www.sql-ex.ru/learn_exercises.php?LN=29)
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

### [Exercise №30](https://www.sql-ex.ru/learn_exercises.php?LN=30)
```
WITH all_transactions as (
  SELECT DISTINCT point,date FROM Income
  UNION
  SELECT DISTINCT point,date FROM Outcome --19 строк
  ),
Income_grouped as (
  SELECT point, date, sum(inc) as sum_inc FROM Income
  GROUP BY point, date
  ),
Outcome_grouped as (
  SELECT point, date, sum(out) as sum_out FROM Outcome
  GROUP BY point, date
  )

SELECT a.point, a.date, o.sum_out, i.sum_inc
FROM all_transactions as a
LEFT JOIN Income_grouped as i ON a.point=i.point AND a.date=i.date
LEFT JOIN Outcome_grouped as o ON a.point=o.point AND a.date=o.date
```

### [Exercise №35](https://www.sql-ex.ru/learn_exercises.php?LN=35)
```
SELECT model, type
FROM
    Product
WHERE
    model NOT LIKE '%[^0-9]%' OR
    model NOT LIKE '%[^A-Za-z]%'
```

### [Exercise №36](https://www.sql-ex.ru/learn_exercises.php?LN=36)
```
SELECT DISTINCT ship as name
FROM Outcomes 
WHERE ship IN ( SELECT DISTINCT class FROM classes) --нашли 2
UNION
SELECT DISTINCT name
FROM ships
WHERE name=class --нашли 7 головных
```

### [Exercise №37](https://www.sql-ex.ru/learn_exercises.php?LN=37)
```
WITH ships_all as (
    SELECT DISTINCT ship as name, ship as class
    FROM Outcomes 
    WHERE ship IN (
                   SELECT DISTINCT class FROM classes)
    UNION
    SELECT DISTINCT name, class
    FROM ships
)

SELECT class
FROM (
      SELECT DISTINCT name, class
      FROM ships_all
     ) as t1
GROUP BY class
HAVING count(name) = 1
```

### [Exercise №39](https://www.sql-ex.ru/learn_exercises.php?LN=39)
```
SELECT DISTINCT ship
FROM (
     SELECT a.ship, a.result, b.date,
            max(date) OVER (PARTITION BY ship) as max_date
     FROM outcomes as a
     JOIN Battles as b ON a.battle=b.name
     ) as all_data
WHERE date < max_date AND
      result='damaged'
```

### [Exercise №40](https://www.sql-ex.ru/learn_exercises.php?LN=40)
```
SELECT maker,  max(type) type
FROM product
GROUP BY maker
HAVING count(DISTINCT type)=1 AND count(model)>  1
```

### [Exercise №41](https://www.sql-ex.ru/learn_exercises.php?LN=41)
```
WITH all_models as (
SELECT model, price FROM PC
UNION
SELECT model, price FROM Laptop
UNION                
SELECT model, price FROM Printer
)

SELECT b.maker, 
       CASE WHEN count(a.model) = count(a.price) THEN max(a.price)
            ELSE NULL
       END as max_price
FROM all_models as a
JOIN product as b ON a.model = b.model
GROUP BY b.maker
```

### [Exercise №43](https://www.sql-ex.ru/learn_exercises.php?LN=43)
```
SELECT DISTINCT name 
FROM battles
WHERE year(date) NOT IN (
                        SELECT launched FROM ships WHERE launched IS NOT NULL
                        ) OR
      year(date) IS NULL
```

### [Exercise №44](https://www.sql-ex.ru/learn_exercises.php?LN=43)
```
SELECT DISTINCT name
FROM ships
WHERE substring(name,1,1)='R'
UNION
SELECT DISTINCT ship
FROM outcomes
WHERE substring(ship,1,1)='R'
```

### [Exercise №45](https://www.sql-ex.ru/learn_exercises.php?LN=45)
```
SELECT name
FROM Ships
WHERE name LIKE '% % %'
UNION
SELECT ship
FROM Outcomes
WHERE ship LIKE '% % %'
```

### [Exercise №46](https://www.sql-ex.ru/learn_exercises.php?LN=46)
```
WITH all_ships as (
SELECT name, class FROM ships
UNION 
SELECT ship, ship FROM outcomes
WHERE ship IN (SELECT class FROM ships)
)

SELECT DISTINCT outcomes.ship, classes.displacement, classes.numGuns
FROM outcomes
LEFT JOIN all_ships ON outcomes.ship  = all_ships.name
LEFT JOIN classes ON classes.class = all_ships.class
WHERE outcomes.battle = 'Guadalcanal'
```

### [Exercise №47](https://www.sql-ex.ru/learn_exercises.php?LN=47)
```
WITH all_ships as( 
SELECT name, class FROM ships
UNION
SELECT ship as name, ship as class FROM outcomes
WHERE ship IN (SELECT class FROM classes)
),

all_ships_countries as (
SELECT classes.country, all_ships.name
FROM all_ships 
JOIN classes ON classes.class = all_ships.class
), --полный справочник: страна - корабль

sunk_list as (
SELECT * FROM outcomes 
WHERE result = 'sunk'), --нашли 6 потопленных

all_ships_countries_sunk as (
SELECT a.country, a.name, b.result
FROM all_ships_countries as a
LEFT JOIN sunk_list as b ON b.ship = a.name
)

SELECT country 
FROM all_ships_countries_sunk 
GROUP BY country
HAVING count(*) = count(result)
```

### [Exercise №49](https://www.sql-ex.ru/learn_exercises.php?LN=49)
```
WITH t1 as (
SELECT DISTINCT ship as name, ship as class
FROM outcomes
WHERE ship NOT IN (SELECT name FROM ships)
UNION
SELECT DISTINCT name, class
FROM ships
) -- справочник всех кораблей

SELECT t1.name
FROM t1
JOIN classes as c ON t1.class = c.class
WHERE c.bore = 16.0
```

### [Exercise №50](https://www.sql-ex.ru/learn_exercises.php?LN=50)
```
SELECT DISTINCT o.battle
FROM Outcomes o
JOIN Ships s ON o.ship = s.name
WHERE s.class = 'Kongo'
```

### [Exercise №51](https://www.sql-ex.ru/learn_exercises.php?LN=51)
```
WITH all_ships as (
SELECT name, class FROM ships
UNION
SELECT ship as name, ship as class FROM outcomes
WHERE ship IN (SELECT class FROM classes)
), --полный справочник кораблей

all_ships1 as (
SELECT all_ships.name, classes.displacement, classes.numGuns,
       max(classes.numGuns) OVER (PARTITION BY classes.displacement) as max_guns 
FROM all_ships
JOIN classes ON all_ships.class = classes.class
)

SELECT name FROM all_ships1
WHERE numGuns = max_guns
```

### [Exercise №52](https://www.sql-ex.ru/learn_exercises.php?LN=52)
```
SELECT DISTINCT S.name
FROM Ships S
JOIN Classes C ON S.class = C.class
WHERE (C.country = 'Japan' OR C.country IS NULL)
  AND (C.type = 'bb' OR C.type IS NULL)
  AND (C.numGuns >= 9 OR C.numGuns IS NULL)
  AND (C.bore < 19 OR C.bore IS NULL)
  AND (C.displacement <= 65000 OR C.displacement IS NULL)
```

### [Exercise №53](https://www.sql-ex.ru/learn_exercises.php?LN=53)
```
SELECT ROUND(AVG(numGuns), 2)
FROM classes
WHERE type = 'bb';
```

### [Exercise №57](https://www.sql-ex.ru/learn_exercises.php?LN=57)
```
WITH t1 as (
SELECT class, name, result
FROM (SELECT name, class FROM ships
      UNION
      SELECT ship as name, ship as clacc FROM outcomes
      WHERE ship in (SELECT class FROM classes) 
      ) Q --полная база кораблей и классов
LEFT JOIN Outcomes 
ON ship=name  AND result = 'sunk' --приджоинили результат, если он sunk
)

SELECT class, count(result) 
FROM t1
GROUP BY class
HAVING count(name) >=3 AND count(result) >0
```

### [Exercise №58](https://www.sql-ex.ru/learn_exercises.php?LN=58)
```
WITH t1 as (
SELECT maker, type, count(model) as models_in_type
FROM product
GROUP BY maker, type
),

t2 as (
SELECT DISTINCT product.maker, t3.type FROM product
cross JOIN (SELECT DISTINCT type FROM product) as t3
),

t4 as (
SELECT maker, count(model) as models_in_maker
FROM product
GROUP BY maker
) 

--скрестить t4 & t2 & t1
SELECT t2.maker, t2.type, --coalesce(t1.models_in_type, 0) as models_in_type, 
       CAST(COALESCE(t1.models_in_type, 0) * 100.00 / t4.models_in_maker AS DECIMAL(10, 2)) AS prc
FROM t2
LEFT JOIN t4 ON t2.maker = t4.maker 
LEFT JOIN t1 ON t2.maker = t1.maker AND t2.type = t1.type
```

### [Exercise №59](https://www.sql-ex.ru/learn_exercises.php?LN=59)
```
WITH t1 AS (
  SELECT point, date, inc 
  FROM Income_o
  UNION
  SELECT point, date, -out 
  FROM Outcome_o
)
SELECT point, SUM(inc) AS total_inc
FROM t1
GROUP BY point
```

### [Exercise №60](https://www.sql-ex.ru/learn_exercises.php?LN=60)
```
WITH t1 AS (
  SELECT point, date, inc 
  FROM Income_o
  UNION
  SELECT point, date, -out 
  FROM Outcome_o
)
SELECT point, SUM(inc) AS total_income
FROM t1
WHERE date < '2001-04-15'
GROUP BY point
```

### [Exercise №61](https://www.sql-ex.ru/learn_exercises.php?LN=61)
```
SELECT coalesce(sum(coalesce(inc,0)),0)-
(SELECT coalesce(sum(coalesce(out,0)),0) FROM Outcome_o)
FROM Income_o
```

### [Exercise №62](https://www.sql-ex.ru/learn_exercises.php?LN=62)
```
WITH t1 AS (
  SELECT point, date, inc AS money
  FROM Income_o
  UNION
  SELECT point, date, -out AS money
  FROM Outcome_o
  ORDER BY point, date
)

SELECT SUM(money)
FROM t1
WHERE date < '2001-04-15 00:00:00'
```

### [Exercise №63](https://www.sql-ex.ru/learn_exercises.php?LN=63)
```
SELECT p.name
FROM Passenger p
WHERE p.id_psg IN (
  SELECT pit.id_psg
  FROM Pass_in_trip pit
  GROUP BY pit.id_psg, pit.place
  HAVING COUNT(pit.place) >= 2
)
```

### [Exercise №64](https://www.sql-ex.ru/learn_exercises.php?LN=64)
```
WITH t as (
SELECT point, date, inc, 0 AS out FROM Income
UNION ALL
SELECT point, date, 0, out FROM Outcome
)

SELECT t.point, t.date, 
       CASE WHEN SUM(t.inc) > 0 THEN 'inc' ELSE 'out' END, 
       CASE WHEN SUM(t.inc) > 0 THEN SUM(t.inc) ELSE SUM(t.out) END 
FROM t 
GROUP BY t.point, t.date 
HAVING SUM(t.inc) = 0 OR 
       SUM(t.out) = 0
```

### [Exercise №65](https://www.sql-ex.ru/learn_exercises.php?LN=65)
```
WITH t1 as (
SELECT DISTINCT maker, type,
       CASE WHEN type = 'PC' THEN 1
            WHEN type = 'Laptop' THEN 2
            WHEN type = 'Printer' THEN 3
            ELSE 0
       END as type_sort
FROM product
)

SELECT ROW_NUMBER() OVER (ORDER BY maker, type_sort) as num, 
       CASE WHEN LAG(maker) OVER (ORDER BY maker) = maker THEN ''
	        ELSE maker
       END as maker,
       type
FROM t1
```

### [Exercise №69](https://www.sql-ex.ru/learn_exercises.php?LN=69)
```
WITH t1 as (
SELECT point, date, inc FROM income
UNION ALL
SELECT point, date, -out FROM outcome
)

SELECT point, CONVERT(VARCHAR, date, 103), 
       sum(inc) OVER (PARTITION BY point ORDER BY date
	                  ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
                     ) as cumulative_dif
FROM (SELECT point, date, sum(inc) as inc FROM t1 GROUP BY point, date) as t3
ORDER BY point, date
```

### [Exercise №100](https://www.sql-ex.ru/learn_exercises.php?LN=100)
```
WITH i as (
SELECT code, point, date, inc,
       ROW_NUMBER() OVER (PARTITION BY date ORDER BY code) as rn
FROM Income
),

o as (
SELECT code, point, date, out,
       ROW_NUMBER() OVER (PARTITION BY date ORDER BY code) as rn
FROM Outcome
)

SELECT coalesce(i.date,o.date),  coalesce(i.rn, o.rn),
       i.point, i.inc, o.point, o.out
FROM i
FULL JOIN o ON i.date = o.date AND i.rn = o.rn
```

### [Exercise №101](https://www.sql-ex.ru/learn_exercises.php?LN=101)
```
WITH t1 as (
SELECT code, model, color, type, price,
       CASE WHEN code = 1 THEN 1 
            WHEN color = 'n' THEN 1
            ELSE 0
       END as new_group_sign
FROM printer
),

t2 as (
SELECT code, model, color, type, price,
       sum(new_group_sign) OVER (ORDER BY code
		  ROWS BETWEEN UNBOUNDED PRECEDING
		  AND CURRENT ROW
		  ) as group_num
FROM t1
),

t3 as (
SELECT group_num, count(DISTINCT type) as distincttypes_cou,
       avg(price) as avg_price
FROM t2
GROUP BY group_num
)

SELECT code, model, color, type, price, 
       max(model) OVER (PARTITION BY t2.group_num) as max_model,
       distincttypes_cou, avg_price
FROM t2 JOIN t3 ON t2.group_num = t3.group_num
```


### [Exercise №1 (DML)](https://www.sql-ex.ru/dmlexercises.php?N=1)
```
INSERT INTO PC 
VALUES (20, 2111, 950,512,60,'52x', 1100);
```

### [Exercise №2 (DML)](https://www.sql-ex.ru/dmlexercises.php?N=2)
```
INSERT INTO Product 
VALUES ('Z', 4003, 'Printer'),
('Z', 4001, 'PC'),
('Z', 4002, 'Laptop')
```
