### Protocol Buffers (Protobuf) vs JSON

Protobuf is smaller because of it's binary encoding and schema driven approach that eliminates redundant data  

|                 | JSON                | Protobuf                  |
| --------------- | ------------------- | ------------------------- |
| Field Names     | Repeated as Strings | Integer Tags              |
| Integers        | ASCII Digits        | Variant Encoding (Varint) |
| Schema Required | No                  | Yes                       |
| Human Readable  | Yes                 | No                        |
## SQL 

SFW Query - Select, From, Where
- SQL cmds are case insensitive but values are not.
- Use single quotes for constants `'abc'`

Like command: Used for pattern matching
```sql
where pname like '%gizmo'
```
% - any sequence of characters
_ - any single character

Distinct - eliminates duplicates
Order By - sorts results (ascending is default). Ties are broken by second attribute in the order by statement
Limit - limits outputs
Foreign Key - common column in 2 or more tables that hat acts as a form of link between two tables
```sql
PRIMARY KEY (student_id, cid), FOREIGN KEY (student_id) REFERENCES Students(cuid)
```

Joins - returns unique combinations of tuples which meet certain conditions (inner, left, right, outer)
Aggregations - SUM, COUNT, MIN, MAX, AVG

**Query Semantics:**
- Compute FROM and WHERE clauses
- Group by the attributes in the GROUP BY
- Compute the SELECT clause

Having - contains conditions on aggregates. Where only has conditions on individual tuples/columns
### More on Joins
- Inner Joins (default)
	- can be written in the following ways
```sql
	  JOIN Purchase ON Product.name = Purchase.prodName
	  %% or %%
	  NNER JOIN Purchase ON Product.name = Purchase.prodName
```
- Outer Joins
	- Returns tuples from joined relations that don't have corresponding tuple in other relations
	- Will include nulls
	- Types - Left outer join, Right outer join, Full outer join
```sql
LEFT OUTER JOIN Purchase ON Product.name = Purchase.prodName
```

### Nested Queries
Used to construct powerful query chains: `f(g(...(x)))`
- Output of one query can be input to another
- Sub-querying returns relations
- Where condition IN ( Subquery )
```sql
SELECT Company.city 
FROM Company 
WHERE Company.name IN ( 
	SELECT Product.manufacturer 
	FROM Purchase, Product 
	WHERE Purchase.product = Product.name 
		AND Purchase.buyer = ‘Alice‘)
```
Subqueries return relations. Operations of the form can also be sued
- `S > ALL R`
- `S < ANY R`
- `EXISTS R`

Complex Correlated Query - They are very powerful and harder to optimize
```sql
%% Find products (and their manufacturers) that are more expensive than all products made by the same manufacturer before 1972 %%
SELECT DISTINCT x.name, x.maker 
FROM Product AS x 
WHERE x.price > ALL( 
	SELECT y.price 
	FROM Product AS y 
	WHERE x.maker = y.maker 
AND y.year < 1972)
```

To aggregate inside nested queries
- Break down query descriptions to steps or subproblems
- Go back to definition when in doubt
Example:
```sql
%% Using a single SQL query, find all of the stations that had the highest daily precipitation (across all stations) on any given day. %%

SELECT station_id, day 
FROM Precipitation, 
		(SELECT day AS maxd, MAX(precipitation) AS maxp 
		FROM precipitation GROUP BY day) 
WHERE day = maxd AND precipitation = maxp
```

Step 1: Find the inner query
Step 2: Join the inner query with the outer (treated as a join?)
