# MySQL 时间分段统计

将表中时间按照指定的时间间隔进行统计

- 方法1

    样例描述：gender为性别字段，1/2分别为男和女，D_DATE为时间字段，现在需要按照每个月为单位进行统计。
    ```sql
    SELECT
        count(IF(GENDER = 1, TRUE, NULL)) AS 男,
        count(IF(GENDER = 2, TRUE, NULL)) AS 女,
        count(IF(GENDER = 0, TRUE, NULL)) AS 未知,
        COUNT(*) AS 总和,
        date_add(
            '2000-01-01 00:00:00',
            INTERVAL floor(
                timestampdiff(
                    MONTH,
                    '2000-01-01 00:00:00',
                    `D_DATE`
                )
            ) MONTH
        )
    FROM
        MediInternalData -- where GENDER = 1
    GROUP BY
        floor(
            timestampdiff(
                MONTH,
                '2000-01-01 00:00:00',
                `D_DATE`
            )
        )
    ```
    2000-01-01是开始时间，下面的一个例子是按照30分钟进行统计
    ```sql
    SELECT
	count(IF(GENDER = 1, TRUE, NULL)) AS 男,
	count(IF(GENDER = 2, TRUE, NULL)) AS 女,
	count(IF(GENDER = 0, TRUE, NULL)) AS 未知,
	COUNT(*) AS 总和,
	date_add(
		'2000-01-01 00:00:00',
		INTERVAL 30 * floor(
			timestampdiff(
				MINUTE,
				'2000-01-01 00:00:00',
				`D_DATE`
			) / 30
		) MINUTE
	)
    FROM
	    MediInternalData -- where GENDER = 1
    GROUP BY
        floor(
            timestampdiff(
                MINUTE,
                '2000-01-01 00:00:00',
                `D_DATE`
            ) / 30
        ) 
    ```
- 方法2

    ```sql
    SELECT
        count(IF(GENDER = 1, TRUE, NULL)) AS 男,
        count(IF(GENDER = 2, TRUE, NULL)) AS 女,
        count(IF(GENDER = 0, TRUE, NULL)) AS 未知,
        count(*) AS 总和,
        date_format(D_DATE, '%Y-%m')
    FROM
        MediInternalData
    GROUP BY
        date_format(D_DATE, '%Y-%m');
    ```