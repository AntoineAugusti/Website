# Daily, Weekly or Monthly Active Users

DAU, WAU and MAU are pretty easy to calculate once you define your user groups (daily, weekly...) and calculate the dates for each group. Visualizations are always recommended for this type of data.

Here is a step by step example for this type of query in PostgreSQL - you can [view it in our demo account](http://demo.redash.io/queries/3231/source#4315) as well.

> #### `WITH` Clause
>
>We used the `WITH` clause in this example - `WITH` clauses allow you to name a sub-query block, this way your query is modular (and sometimes runs faster), it can later be referred to inside the main query instead of making a large main query with many aliases, `JOIN`s  or other complexities. You can have multiple sub-queries, just be sure to have a coma between them.

1. Define each group or users (daily, weekly and monthly - or any combination that suits you).

  Define the smallest time range first as you'll use it in the other time ranges - in this case it's the daily users (DAU) and you'll group them by the creation date ("age").

  Also define what an active user is - in this case we count an active user by the first time we saw its id in the events table.

      ```sql
      WITH
      dau AS (
            SELECT created_at::DATE AS "date", count(distinct user_id) AS dau
            FROM events
            WHERE created_at > '2016-10-01'
            GROUP BY 1
           )
      ```

2. Calculate the dates of each group - use relative dates and exact ones to keep your dataset tidy and your query speedy.

      ```sql
      SELECT "date", dau,
             (SELECT count(distinct user_id)
              FROM events
              WHERE events.created_at::DATE BETWEEN dau.date - 29 AND dau.date
              AND created_at > '2016-10-01'
             ) AS mau,
             (SELECT count(distinct user_id)
              FROM events
              WHERE events.created_at::DATE BETWEEN dau.date - 7 AND dau.date
              AND created_at > '2016-10-01'
             ) AS wau
      FROM dau
      ```

3. Select a nifty visualization

![](../assets/visualization_examples/dau_wau_mau.png)
