### Description
Given the schema presented below write a query, which uses a window function, that returns two most viewed posts for every category.

Order the result set by:
* **category name** alphabetically
* **number of post views** largest to lowest
* **post id** lowest to largest

Note:
Some categories may have less than two or no posts at all.
Two or more posts within the category can be tied by (have the same) the number of views. 
Use post id as a tie breaker - a post with a lower id gets a higher rank.

### Schema
**categories**
```
 Column     | Type                        | Modifiers
------------+-----------------------------+----------
id          | integer                     | not null
category    | character varying(255)      | not null
```
**posts**
```
 Column     | Type                        | Modifiers
------------+-----------------------------+----------
id          | integer                     | not null
category_id | integer                     | not null
title       | character varying(255)      | not null
views       | integer                     | not null
```
**SQL запрос:**
```SQL
WITH sub_q AS
(
SELECT 
  categories.id AS category_id,
  category, 
  title, 
  views, 
  posts.id AS post_id,
  ROW_NUMBER() OVER (PARTITION BY category_id ORDER BY views DESC, posts.id ASC) AS rank
FROM posts RIGHT JOIN categories ON categories.id = posts.category_id
)
SELECT category_id, category, title, views, post_id
FROM sub_q
WHERE rank < 3
ORDER BY category, views DESC, post_id ASC
