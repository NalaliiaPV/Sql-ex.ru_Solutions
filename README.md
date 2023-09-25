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

