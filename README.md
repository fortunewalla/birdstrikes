# smalldata-training

## Selecting data

Select all data

`SELECT * FROM birdstrikes;`

Select all & limit

`SELECT * FROM birdstrikes LIMIT 10;`

Select certain fields

`SELECT bird_size, cost FROM birdstrikes LIMIT 10;`

## Ordering data

Order by a field

`SELECT state, cost FROM birdstrikes ORDER BY cost LIMIT 10;`

Order by a multiple fields

`SELECT state, cost FROM birdstrikes ORDER BY state, cost LIMIT 10;`

Order by a multiple fields

`SELECT state, cost FROM birdstrikes ORDER BY state, cost LIMIT 10;`

Reverse ordering

`SELECT state, cost FROM birdstrikes ORDER BY cost DESC;`

Reverse ordering by multple fields

`SELECT state, cost FROM birdstrikes ORDER BY state DESC, cost;`

## Renaming fields
`SELECT bird_size as size, state FROM birdstrikes;`

```
SELECT
    state || ' - ' || flight_date as title,
    cost
FROM birdstrikes
ORDER BY cost DESC;
```

## Filtering data
Filter by field value

`SELECT * FROM birdstrikes WHERE state = 'Alabama';`

Filter by multiple conditions

`SELECT * FROM birdstrikes WHERE state = 'Alabama' AND bird_size = 'Small';`

`SELECT * FROM birdstrikes WHERE state = 'Alabama' OR state = 'Missouri';`

`SELECT * FROM birdstrikes WHERE state IN ('Alabama', 'Missouri');`

String operations:

`SELECT state FROM birdstrikes WHERE state LIKE 'A%' OR state LIKE '%a';`

`SELECT state FROM birdstrikes WHERE LOWER(state) = 'alabama';`

Lowercase: `LOWER`
Uppercase: `UPPER`

```
SELECT EXTRACT(DOW FROM flight_date) as day_of_week, * FROM birdstrikes;
```

It can go complicated:

```
SELECT state, cost, bird_size FROM birdstrikes
WHERE (LOWER(state) = 'alabama'
        OR LOWER(state) = 'missoury')
      AND (bird_size = 'Small')
ORDER BY cost DESC;
```

## Removing duplicates
`SELECT DISTINCT state, size FROM birdstrikes;`

## WORKSHOP
* Hány dollár volt a legtöbb kár?
* ^Ez melyik államban történt és milyen madár okozta?
* Melyik az első három állam ABC sorrendben?
* Milyen méretű madarak vannak az adatbázisban?
* Mekkora méretű madár okozta a legnagyobb kárt Missouri-ban?
* Hány államban történt baleset?
* A hét melyik napján történt a legnagyobb kár?

* How many dollars was the most damage?
* ^In what state did this happen and what kind of bird caused it?
* Which are the first three states in ABC order?
* What size birds are in the database?
* What size bird caused the most damage in Missouri?
* In how many states did the accident occur?
* On which day of the week did the greatest damage occur?

* What's the maximum overall cost
* ^^ In which state did this accident happen?
* Display the first three states in alphabetical order?
* Display the bird sizes (don't display the "empty" cell)
* What is the size of the bird that caused the biggest damage in Missouri?

## SOME more DML

Updating
```
UPDATE birdstrikes SET aircraft='Unknown' WHERE aircraft IS NULL;
```

Deleting
```
DELETE FROM birdstrikes WHERE aircraft = 'Unknown';
```

Truncating
```
TRUNCATE birdstrikes;
```

## Groupping and aggregation

COUNT(*)
```
SELECT COUNT(*) FROM birdstrikes;
```

Simple aggregations
```
SELECT MAX(cost) FROM birdstrikes;
```

```
SELECT state, MAX(cost) AS max_cost FROM birdstrikes GROUP BY state ORDER BY state;
```

Multiple aggregate functions:
```
SELECT state, aircraft, COUNT(*), MAX(cost), MIN(cost), AVG(cost) FROM birdstrikes WHERE state LIKE 'A%' GROUP BY state, aircraft ORDER BY state, aircraft;
```

**Sometimes it doesn't work**:
```
SELECT aircraft, state, MAX(cost) AS max_cost FROM birdstrikes GROUP BY state ORDER BY state;
```

Let's fix it:
```
SELECT state, aircraft, MAX(cost) AS max_cost FROM birdstrikes GROUP BY state, aircraft ORDER BY state, aircraft;
```

You can filter here, too:
```
SELECT state, aircraft, MAX(cost) AS max_cost FROM birdstrikes WHERE state LIKE 'A%' GROUP BY state, aircraft ORDER BY state, aircraft;
```

Advanced groupping - HAVING
```
SELECT state, COUNT(*) FROM birdstrikes GROUP BY state HAVING COUNT(*) > 100;
```

```
SELECT state, COUNT(*) FROM birdstrikes
WHERE state IS NOT NULL
GROUP BY state HAVING COUNT(*) > 100;
```

## WORKSHOP
 * Mégegyszer: Hány államban történt baleset? (Most egy szám legyen a képernyőn az eredmény)
 * Melyik államban történt a legtöbb összkár
 * Átlagosan melyik méretű madár mekkora kárt okoz?
 * Írasd ki madár és állam-méretenként (tehát 2 mezőn együttesen csoportosítva) a legnagyobb kár értékét.
 * ^^ Ebből irasd ki az átlagos kárt azokban az állam/méret csoportokban, ahol több, mint 1000 baleset történt

* Again: In how many states did the accident occur? (Now the result should be a number on the screen)
* In which state did the most total damage occur?
* On average, what size bird does how much damage?
* Write out the value of the largest damage per bird and state size (ie grouped together in 2 fields).
* ^^ From this, write down the average damage in the states/size groups where more than 1000 accidents occurred

 * Which is the state with the most accidents?
 * List the average (AVG) damage caused by each bird size?
 * List the maximum (MAX) damage caused by each bird size in each state.
 * ^^ Only show those rows from the above solutions which have a bigger damage than $100000

## JOINING
```
SELECT * FROM emberek
JOIN varosok ON (emberek.varos_id = varosok.id);
```

```
SELECT * FROM emberek
INNER JOIN varosok ON (emberek.varos_id = varosok.id);
```

```
SELECT * FROM emberek
LEFT JOIN varosok ON (emberek.varos_id = varosok.id);
```

```
SELECT * FROM emberek
RIGHT JOIN varosok ON (emberek.varos_id = varosok.id);
```

```
SELECT * FROM emberek
FULL OUTER JOIN varosok ON (emberek.varos_id = varosok.id);
```

## Create table as SELECT
```
CREATE TABLE birdstrikes_denorm AS
    SELECT b.*,s.id as state_id
    FROM birdstrikes b
       LEFT JOIN states s ON (b.state = s.state_name);
ALTER TABLE birdstrikes_denorm DROP state;
```

## WORKSHOP
 * Újra: Hány államban történtek balesetek?
 * Nézd meg, hogy hány sort látunk a különböző joinoknál a táblában (INNER, LEFT, RIGHT, FULL OUTER)
 
* Again: In how many states were there accidents?
* See how many rows we see for the different joins in the table (INNER, LEFT, RIGHT, FULL OUTER)

### Using Zeppelin Notebook
```
%psql.sql SELECT EXTRACT(dow FROM flight_date) as week, COUNT(*) FROM birdstrikes GROUP BY week;
```

```
%psql.sql %psql.sql SELECT bird_size, damage, count(*) FROM birdstrikes WHERE bird_size IS NOT NULL and damage IS NOT NULL GROUP BY bird_size, damage;
```
