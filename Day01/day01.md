## Day01

### exercise 00
Please write a SQL statement which returns menu’s identifier and pizza names from `menu` table and person’s identifier and person name from `person` table in one global list (with column names as presented on a sample below) ordered by object_id and then by object_name columns.

Code:
```
SELECT 
    menu.id AS object_id, 
    menu.pizza_name AS object_name 
FROM 
    menu

UNION ALL

SELECT 
    person.id AS object_id, 
    person.name AS object_name 
FROM 
    person

ORDER BY 
    object_id, 
    object_name;
```

![](img/%D0%A1%D0%BD%D0%B8%D0%BC%D0%BE%D0%BA%20%D1%8D%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202025-01-25%20160518.png)


### exercise 01
Please modify a SQL statement from “exercise 00” by removing the object_id column. Then change ordering by object_name for part of data from the `person` table and then from `menu` table (like presented on a sample below). Please save duplicates!

code:

```
SELECT 
    person.name AS object_name 
FROM 
    person

UNION ALL

SELECT 
    menu.pizza_name AS object_name 
FROM 
    menu

ORDER BY 
    CASE 
        WHEN object_name IN (SELECT name FROM person) THEN 0 
        ELSE 1 
    END, 
    object_name;
```

![](img/png1.png)

### exercise 02
Please write a SQL statement which returns unique pizza names from the `menu` table and orders them by pizza_name column in descending mode. Please pay attention to the Denied section.

code:

```
SELECT DISTINCT 
    pizza_name 
FROM 
    menu 
ORDER BY 
    pizza_name DESC;
```

![](img/png2.png)

### exercise 03
Please write a SQL statement which returns common rows for attributes order_date, person_id from `person_order` table from one side and visit_date, person_id from `person_visits` table from the other side (please see a sample below). In other words, let’s find identifiers of persons, who visited and ordered some pizza on the same day. Actually, please add ordering by action_date in ascending mode and then by person_id in descending mode.

code:

```
SELECT 
    po.order_date AS action_date,
    po.person_id 
FROM 
    person_order po
INNER JOIN 
    person_visits pv ON po.person_id = pv.person_id AND po.order_date = pv.visit_date
ORDER BY 
    action_date ASC, 
    po.person_id DESC;
```

![](img/png3.png)

### exercise 04
Please write a SQL statement which returns a difference (minus) of person_id column values with saving duplicates between `person_order` table and `person_visits` table for order_date and visit_date are for 7th of January of 2022

code:

```
SELECT person_id 
FROM person_order 
WHERE order_date = '2022-01-07'

EXCEPT

SELECT person_id 
FROM person_visits 
WHERE visit_date = '2022-01-07';
```

![](img/png4.png)

### exercise 05
Please write a SQL statement which returns all possible combinations between `person` and `pizzeria` tables and please set ordering by person identifier and then by pizzeria identifier columns. Please take a look at the result sample below. Please be aware column's names can be different for you

code:

```
SELECT 
    p.id AS person_identifier, 
    pi.id AS pizzeria_identifier
FROM 
    person p
CROSS JOIN 
    pizzeria pi
ORDER BY 
    p.id, 
    pi.id;
```

![](img/png5.png)

### exercise 06
Let's return our mind back to exercise #03 and change our SQL statement to return person names instead of person identifiers and change ordering by action_date in ascending mode and then by person_name in descending mode. Please take a look at a data sample below.

code:

```
SELECT 
    po.order_date AS action_date, 
    (SELECT name FROM person WHERE id = po.person_id) AS person_name
FROM 
    person_order po
WHERE 
    po.order_date IN (
        SELECT visit_date 
        FROM person_visits 
        WHERE person_id = po.person_id
    )
ORDER BY 
    action_date ASC, 
    person_name DESC;
```

![](img/png6.png)

### exercise 07
Please write a SQL statement which returns the date of order from the `person_order` table and corresponding person name (name and age are formatted as in the data sample below) which made an order from the `person` table. Add a sort by both columns in ascending mode.

code:

```
SELECT 
    po.order_date,
    CONCAT(p.name, ' (age:', p.age, ')') AS person_information
FROM 
    person_order po
INNER JOIN 
    person p ON po.person_id = p.id
ORDER BY 
    po.order_date ASC, 
    person_information ASC;
```

![](img/png7.png)

### exercise 08
Please rewrite a SQL statement from exercise #07 by using NATURAL JOIN construction. The result must be the same like for exercise #07. 

code:

```
SELECT 
    po.order_date,
    CONCAT(p.name, ' (age:', p.age, ')') AS person_information
FROM 
    person_order po
NATURAL JOIN 
    person p
ORDER BY 
    po.order_date ASC, 
    person_information ASC;
```

![](img/png8.png)

### exercise 09
Please write 2 SQL statements which return a list of pizzerias names which have not been visited by persons by using IN for 1st one and EXISTS for the 2nd one.

code:

```
SELECT name 
FROM pizzeria 
WHERE id NOT IN (SELECT DISTINCT pizzeria_id FROM person_visits);
```

![](img/png9.png)

### exercise 10
Please write a SQL statement which returns a list of the person names which made an order for pizza in the corresponding pizzeria. 
The sample result (with named columns) is provided below and yes ... please make ordering by 3 columns (`person_name`, `pizza_name`, `pizzeria_name`) in ascending mode.

code:

```
SELECT 
    p.name AS person_name, 
    m.pizza_name, 
    pi.name AS pizzeria_name
FROM 
    person_order po
JOIN 
    person p ON po.person_id = p.id
JOIN 
    menu m ON po.menu_id = m.id
JOIN 
    pizzeria pi ON m.pizzeria_id = pi.id
ORDER BY 
    p.name, 
    m.pizza_name, 
    pi.name;
```

![](img/png10.png)