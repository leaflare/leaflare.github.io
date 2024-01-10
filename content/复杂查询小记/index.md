+++
title = "复杂查询小记"
date = 2023-01-04 20:37:19
slug = "202301042037"

[taxonomies]
tags = ["SQL" ]
categories = ["SQL"]

+++

<!-- more -->

业务背景：需要给数据行重新生成一个字段，按新字段排序时，相同类别的数据行分散出现（尽可能不连续出现）。

```sql
SELECT
    id,
    category_id,
    ROW_NUMBER() OVER (
        ORDER BY 
        	order_id, 
        	reverse(category_id) 
        ) AS shuffled_id
FROM
(
    SELECT
        id,
        category_id,
        ROW_NUMBER() OVER (
            PARTITION BY 
            	category_id 
            ORDER BY 
            	id
        	) AS order_id
    FROM
        original_table
) AS subquery;

	
	
```

