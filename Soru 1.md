# Soru 1) 1980’den itibaren spor grubu bazında en çok madalya alan 1. 3. 5. ülkeyi bulalım.

```SQL
WITH medal_winners AS
(
  SELECT 
    Sport,
    Country, 
    count(1) as medal_count,
    ROW_NUMBER() OVER (PARTITION BY Sport ORDER BY count(1) DESC) AS rn,
  FROM `dsmbootcamp.selcuk_akarin.summer_medals` 
  WHERE medal IS NOT NULL AND year >= 1980 
  GROUP BY Sport,Country
  ORDER BY Sport,medal_count DESC
)

SELECT ARRAY_AGG(Sport) AS agg_sport,first_champ_country,third_champ_country,fifth_champ_country
FROM(
SELECT
  Sport,
  Country,
  medal_count,
  first_value(Country) OVER(PARTITION BY Sport ORDER BY medal_count DESC ROWS BETWEEN 4 PRECEDING AND 4 FOLLOWING) AS first_champ_country,
  NTH_VALUE (Country,2) OVER(PARTITION BY Sport ORDER BY medal_count DESC ROWS BETWEEN 4 PRECEDING and 4 FOLLOWING) AS third_champ_country,
  NTH_VALUE (Country,3) OVER(PARTITION BY Sport ORDER BY medal_count DESC ROWS BETWEEN 4 PRECEDING and 4 FOLLOWING) AS fifth_champ_country
FROM medal_winners
WHERE rn IN (1,3,5)
ORDER BY Sport
)
GROUP BY first_champ_country,third_champ_country,fifth_champ_country
```
