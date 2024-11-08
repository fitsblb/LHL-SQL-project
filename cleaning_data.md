What issues will you address by cleaning the data?
1-
## I identified several rows in the city and country columns of the new_sessions table that contained values such as '--', 'not set%', or '%not available in demo dataset'. To ensure accuracy throughout my analysis, I applied a filter condition and created a view that excludes these inconsistencies, allowing for more reliable results.

2-
## I have also dropped some columns where the was no values/NULL in the rows. The columns dropped are, searchkeyword,productrefundamount,  itemquantity, itemrevenue all of them are from new_sessions.

3-
## I have found three inconsistencies in productsku column in new_sessions where there was unwanted space in the sKu, so I trimmed out the space by replacing thee space with an empty string

4-
## In question number one, I have noticed the city of New York in correctly associated with Canada as country, so I corrected it to match it's respective country



Queries:
## Below, provide the SQL queries you used to clean your data.



``` SELECT 
        ns.productsku AS product_id,
        p.product_name AS product_name,
        ns.city AS city,
        ns.country AS country,
        COUNT(ns.productsku) AS total_orders,
		RANK() OVER (PARTITION BY country ORDER BY COUNT(ns.productsku) DESC) AS country_product_rank,
		RANK() OVER (PARTITION BY city ORDER BY COUNT(ns.productsku) DESC) AS city_product_rank,
    	DENSE_RANK() OVER (ORDER BY COUNT(ns.productsku) DESC) AS global_rank  -- Rank products globally by popularity
    FROM 
        new_sessions ns
    JOIN 
        products p 
    ON 
        p.sku = ns.productsku
    WHERE
        ns.city IS NOT NULL
        AND ns.country IS NOT NULL
        AND ns.productsku IS NOT NULL
        AND ns.city NOT IN ('(not set)', 'not available in demo dataset')
        AND ns.country NOT IN ('(not set)', 'not available in demo dataset')
        AND ns.productprice IS NOT NULL
    GROUP BY
        ns.productsku,
        p.product_name,
        ns.city,
        ns.country```



```ALTER TABLE new_sessions
DROP COLUMN searchkeyword;```



```UPDATE new_sessions
SET productsku = REPLACE(productsku, ' ', '')
WHERE NOT productsku ~ '^[A-Za-z0-9]+$';--checks sku's with non alphanumerical characters```





CASE
           ``` WHEN TRIM(city) = 'New York' AND TRIM(country) = 'Canada' THEN 'New York'  -- Keep city as New York
            WHEN TRIM(city) IN ('(not set)', 'not available in demo dataset') THEN 'City in ' || TRIM(country)
            ELSE TRIM(city)
        END AS modified_city,
        CASE
            WHEN TRIM(city) = 'New York' AND TRIM(country) = 'Canada' THEN 'United States'  -- Change country to United States
            ELSE TRIM(country)
        END AS country,  -- Trim spaces from country names```








