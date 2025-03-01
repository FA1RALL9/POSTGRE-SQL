## Exercise 00
Please write a SQL statement which returns a list of pizzerias names with corresponding rating value which have not been visited by persons. 

code:

SELECT p.name, p.rating
FROM pizzeria p
LEFT JOIN person_visits v ON p.id = v.pizzeria_id
WHERE v.pizzeria_id IS NULL;

img:
![schema](Day2/img/00.png)

## Exercise 01
Please write a SQL statement which returns the missing days from 1st to 10th of January 2022 (including all days) for visits  of persons with identifiers 1 or 2 (it means days missed by both). Please order by visiting days in ascending mode. The sample of data with column name is presented below.

code:

WITH date_range AS (
    SELECT '2022-01-01'::date + INTERVAL '1 day' * i AS visit_date
    FROM generate_series(0, 9) AS i
)
SELECT dr.visit_date
FROM date_range dr
WHERE NOT EXISTS (
    SELECT 1
    FROM person_visits v
    WHERE v.visit_date = dr.visit_date AND v.person_id IN (1, 2)
    GROUP BY v.visit_date
    HAVING COUNT(DISTINCT v.person_id) = 2
)
ORDER BY dr.visit_date ASC;

img:
![schema](Day2/img/01.png)

## Exercise 02
Please write a SQL statement that returns a whole list of person names visited (or not visited) pizzerias during the period from 1st to 3rd of January 2022 from one side and the whole list of pizzeria names which have been visited (or not visited) from the other side. The data sample with needed column names is presented below. Please pay attention to the substitution value ‘-’ for `NULL` values in `person_name` and `pizzeria_name` columns. Please also add ordering for all 3 columns.

code:

WITH date_range AS (
    SELECT generate_series('2022-01-01'::date, '2022-01-03'::date, '1 day'::interval) AS visit_date
),
all_persons AS (
    SELECT p.id, p.name
    FROM person p
),
all_pizzerias AS (
    SELECT pp.id, pp.name
    FROM pizzeria pp
),
visited AS (
    SELECT v.person_id, v.pizzeria_id
    FROM person_visits v
    WHERE v.visit_date BETWEEN '2022-01-01' AND '2022-01-03'
)
SELECT 
    COALESCE(p.name, '-') AS person_name,
    COALESCE(pp.name, '-') AS pizzeria_name,
    dr.visit_date
FROM date_range dr
CROSS JOIN all_persons p
CROSS JOIN all_pizzerias pp
LEFT JOIN visited v ON p.id = v.person_id AND pp.id = v.pizzeria_id
ORDER BY dr.visit_date, person_name, pizzeria_name;

img:
![schema](Day2/img/02.png)

## Exercise 03
Let’s return back to Exercise #01, please rewrite your SQL by using the CTE (Common Table Expression) pattern. Please move into the CTE part of your "day generator". The result should be similar like in Exercise #01

code:

WITH date_range AS (
    SELECT '2022-01-01'::date + INTERVAL '1 day' * i AS visit_date
    FROM generate_series(0, 9) AS i  -- Генерирует числа от 0 до 9
),
visited_days AS (
    SELECT v.visit_date
    FROM person_visits v
    WHERE v.visit_date BETWEEN '2022-01-01' AND '2022-01-10' AND v.person_id IN (1, 2)
    GROUP BY v.visit_date
    HAVING COUNT(DISTINCT v.person_id) = 2
)
SELECT dr.visit_date
FROM date_range dr
WHERE NOT EXISTS (
    SELECT 1
    FROM visited_days vd
    WHERE vd.visit_date = dr.visit_date
)
ORDER BY dr.visit_date ASC;

img:
![schema](Day2/img/03.png)

## Exercise 04
Find full information about all possible pizzeria names and prices to get mushroom or pepperoni pizzas. Please sort the result by pizza name and pizzeria name then. The result of sample data is below (please use the same column names in your SQL statement).

code:

SELECT 
    m.pizza_name AS pizza_name,
    m.price AS pizza_price,
    p.name AS pizzeria_name
FROM 
    menu m
JOIN 
    pizzeria p ON m.pizzeria_id = p.id
WHERE 
    m.pizza_name ILIKE '%mushroom%' OR m.pizza_name ILIKE '%pepperoni%'
ORDER BY 
    pizza_name, pizzeria_name;

img:
![schema](Day2/img/04.png)

## Exercise 05
Find names of all female persons older than 25 and order the result by name. The sample of output is presented below.

code:

SELECT 
    name
FROM 
    person
WHERE 
    gender = 'female' AND age > 25
ORDER BY 
    name;

img:
![schema](Day2/img/05.png)

## Exercise 06
Please find all pizza names (and corresponding pizzeria names using `menu` table) that Denis or Anna ordered. Sort a result by both columns. The sample of output is presented below.

code:

SELECT 
    m.pizza_name AS pizza_name,
    p.name AS pizzeria_name
FROM 
    person_order po
JOIN 
    person pe ON po.person_id = pe.id
JOIN 
    menu m ON po.menu_id = m.id
JOIN 
    pizzeria p ON m.pizzeria_id = p.id
WHERE 
    pe.name IN ('Denis', 'Anna')
ORDER BY 
    m.pizza_name, p.name;

img:
![schema](Day2/img/06.png)

## Exercise 07
Please find the name of pizzeria Dmitriy visited on January 8, 2022 and could eat pizza for less than 800 rubles.

code:

SELECT 
    p.name AS pizzeria_name
FROM 
    person_visits v
JOIN 
    person pe ON v.person_id = pe.id
JOIN 
    menu m ON v.pizzeria_id = m.pizzeria_id
JOIN 
    pizzeria p ON m.pizzeria_id = p.id
WHERE 
    pe.name = 'Dmitriy' 
    AND v.visit_date = '2022-01-08' 
    AND m.price < 800
GROUP BY 
    p.name;

img:
![schema](Day2/img/07.png)

## Exercise 08
Please find the names of all males from Moscow or Samara cities who orders either pepperoni or mushroom pizzas (or both) . Please order the result by person name in descending mode. The sample of output is presented below.

code:

SELECT 
    p.name
FROM 
    person p
JOIN 
    person_order o ON p.id = o.person_id
JOIN 
    menu m ON o.menu_id = m.id
WHERE 
    p.gender = 'male' 
    AND (p.address IN ('Moscow', 'Samara'))
    AND (m.pizza_name IN ('pepperoni pizza', 'mushroom pizza'))
ORDER BY 
    p.name DESC;

img:
![schema](Day2/img/08.png)

## Exercise 09
Please find the names of all females who ordered both pepperoni and cheese pizzas (at any time and in any pizzerias). Make sure that the result is ordered by person name. The sample of data is presented below.

code:

SELECT 
    p.name
FROM 
    person p
JOIN 
    person_order o ON p.id = o.person_id
JOIN 
    menu m ON o.menu_id = m.id
WHERE 
    p.gender = 'female' 
    AND m.pizza_name IN ('pepperoni pizza', 'cheese pizza')
ORDER BY 
    p.name;

img:
![schema](Day2/img/09.png)