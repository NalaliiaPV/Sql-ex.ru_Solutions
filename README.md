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

### [Exercises №39](https://www.sql-ex.ru/learn_exercises.php?LN=39)
```
select distinct ship
from (
     select a.ship, a.result, b.date,
            max(date) over (partition by ship) as max_date
     from outcomes as a
     join Battles as b on a.battle=b.name
     ) as all_data
where date < max_date AND
      result='damaged'
```

### [Exercises №40](https://www.sql-ex.ru/learn_exercises.php?LN=40)
```
SELECT maker,  max(type) type
FROM product
GROUP BY maker
HAVING count(distinct type)=1 AND count(model)>  1
```

### [Exercises №41](https://www.sql-ex.ru/learn_exercises.php?LN=41)
```
with all_models as (
select model, price from PC
UNION
select model, price from Laptop
UNION                
select model, price from Printer
)

select b.maker, 
       case when count(a.model) = count(a.price) then max(a.price)
            else NULL
       End as max_price
from all_models as a
join product as b ON a.model = b.model
group by b.maker
```

### [Exercises №43](https://www.sql-ex.ru/learn_exercises.php?LN=43)
```
Select distinct name 
from battles
where year(date) NOT IN (
                        select launched from ships WHERE launched IS NOT NULL
                        ) OR
      year(date) IS NULL
```

### [Exercises №44](https://www.sql-ex.ru/learn_exercises.php?LN=43)
```
Select distinct name
from ships
where substring(name,1,1)='R'
union
Select distinct ship
from outcomes
where substring(ship,1,1)='R'
```

### [Exercises №45](https://www.sql-ex.ru/learn_exercises.php?LN=45)
```
SELECT name
FROM Ships
WHERE name LIKE '% % %'
UNION
SELECT ship
FROM Outcomes
WHERE ship LIKE '% % %'
```

### [Exercises №46](https://www.sql-ex.ru/learn_exercises.php?LN=46)
```
with all_ships as (
select name, class from ships
union 
select ship, ship from outcomes
where ship IN (select class from ships)
)

select distinct outcomes.ship, classes.displacement, classes.numGuns
from outcomes
left join all_ships ON outcomes.ship  = all_ships.name
left join classes ON classes.class = all_ships.class
where outcomes.battle = 'Guadalcanal'
```

### [Exercises №47](https://www.sql-ex.ru/learn_exercises.php?LN=47)
```
with all_ships as( 
Select name, class from ships
union
select ship as name, ship as class from outcomes
where ship IN (select class from classes)
),

all_ships_countries as (
select classes.country, all_ships.name
from all_ships 
join classes on classes.class = all_ships.class
), --полный справочник: страна - корабль

sunk_list as (
select * from outcomes 
where result = 'sunk'), --нашли 6 потопленных

all_ships_countries_sunk as (
select a.country, a.name, b.result
from all_ships_countries as a
left join sunk_list as b on b.ship = a.name
)

select country 
from all_ships_countries_sunk 
group by country
having count(*) = count(result)
```

### [Exercises №49](https://www.sql-ex.ru/learn_exercises.php?LN=49)
```
with t1 as (
Select distinct ship as name, ship as class
from outcomes
where ship not in (select name from ships)
union
Select distinct name, class
from ships
) -- справочник всех кораблей

select t1.name
from t1
join classes as c on t1.class = c.class
where c.bore = 16.0
```

### [Exercises №51](https://www.sql-ex.ru/learn_exercises.php?LN=51)
```
with all_ships as (
select name, class from ships
union
select ship as name, ship as class from outcomes
where ship IN (select class from classes)
), --полный справочник кораблей

all_ships1 as (
select all_ships.name, classes.displacement, classes.numGuns,
       max(classes.numGuns) over (partition by classes.displacement) as max_guns 
from all_ships
join classes ON all_ships.class = classes.class
)

select name from all_ships1
where numGuns = max_guns
```

### [Exercises №52](https://www.sql-ex.ru/learn_exercises.php?LN=52)
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

### [Exercises №58](https://www.sql-ex.ru/learn_exercises.php?LN=58)
```
with t1 as (
select maker, type, count(model) as models_in_type
from product
group by maker, type
),

t2 as (
select distinct product.maker, t3.type from product
cross join (select distinct type from product) as t3
),

t4 as (
select maker, count(model) as models_in_maker
from product
group by maker
) 

--скрестить t4 & t2 & t1
select t2.maker, t2.type, --coalesce(t1.models_in_type, 0) as models_in_type, 
       CAST(COALESCE(t1.models_in_type, 0) * 100.00 / t4.models_in_maker AS DECIMAL(10, 2)) AS prc
from t2
left join t4 on t2.maker = t4.maker 
left join t1 on t2.maker = t1.maker AND t2.type = t1.type
```

### [Exercises №57](https://www.sql-ex.ru/learn_exercises.php?LN=57)
```
with t1 as (
SELECT class, name, result
FROM (Select name, class from ships
      union
      select ship as name, ship as clacc from outcomes
      where ship in (select class from classes) 
      ) Q --полная база кораблей и классов
LEFT JOIN Outcomes 
ON ship=name  AND result = 'sunk' --приджоинили результат, если он sunk
)

select class, count(result) 
from t1
group by class
having count(name) >=3 AND count(result) >0
```

### [Exercises №61](https://www.sql-ex.ru/learn_exercises.php?LN=61)
```
SELECT coalesce(sum(coalesce(inc,0)),0)-
(SELECT coalesce(sum(coalesce(out,0)),0) FROM Outcome_o)
FROM Income_o
```

### [Exercises №65](https://www.sql-ex.ru/learn_exercises.php?LN=65)
```
with t1 as (
Select distinct maker, type,
       case when type = 'PC' then 1
            when type = 'Laptop' then 2
            when type = 'Printer' then 3
            else 0
       end as type_sort
from product
)

select ROW_NUMBER() OVER (order by maker, type_sort) as num, 
       case WHEN LAG(maker) OVER (ORDER BY maker) = maker THEN ''
	        ELSE maker
       end as maker,
       type
from t1
```

### [Exercises №69](https://www.sql-ex.ru/learn_exercises.php?LN=69)
```
with t1 as (
select point, date, inc from income
union all
select point, date, -out from outcome
)

select point, CONVERT(VARCHAR, date, 103), 
       sum(inc) over (partition by point order by date
	                  rows between unbounded preceding and current row
                     ) as cumulative_dif
from (select point, date, sum(inc) as inc from t1 group by point, date) as t3
order by point, date
```

### [Exercises №100](https://www.sql-ex.ru/learn_exercises.php?LN=100)
```
with i as (
select code, point, date, inc,
       ROW_NUMBER() OVER (partition by date order by code) as rn
from Income
),

o as (
select code, point, date, out,
       ROW_NUMBER() OVER (partition by date order by code) as rn
from Outcome
)

select coalesce(i.date,o.date),  coalesce(i.rn, o.rn),
       i.point, i.inc, o.point, o.out
from i
full join o on i.date = o.date AND i.rn = o.rn
```

### [Exercises №101](https://www.sql-ex.ru/learn_exercises.php?LN=101)
```
with t1 as (
Select code, model, color, type, price,
       case when code = 1 then 1 
            when color = 'n' then 1
            else 0
       end as new_group_sign
from printer
),

t2 as (
select code, model, color, type, price,
       sum(new_group_sign) over (order by code
		  rows between unbounded preceding
		  and current row
		  ) as group_num
from t1
),

t3 as (
select group_num, count(distinct type) as distinct_types_cou,
       avg(price) as avg_price
from t2
group by group_num
)

select code, model, color, type, price, 
       max(model) over (partition by t2.group_num) as max_model,
       distinct_types_cou, avg_price
from t2 join t3 on t2.group_num = t3.group_num
```


### [Exercises №1 (DML](https://www.sql-ex.ru/dmlexercises.php?N=1)
```
INSERT INTO PC 
VALUES (20, 2111, 950,512,60,'52x', 1100);
```

### [Exercises №2 (DML](https://www.sql-ex.ru/dmlexercises.php?N=2)
```
INSERT INTO Product 
VALUES ('Z', 4003, 'Printer'),
('Z', 4001, 'PC'),
('Z', 4002, 'Laptop')
```
