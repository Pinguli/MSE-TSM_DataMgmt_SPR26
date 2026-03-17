# Eskuel Tutorial Draft – SQL Island: The Tower of Rankings

## Tutorial title
**SQL Island: The Tower of Rankings — A Cosmic Rescue Quest**

## Short intro / opening text
Far beyond the usual tables and transactions, in the middle of the Digital Ocean, lies a mysterious land known as **SQL Island**. At its centre stands a stone signal tower built by the **Data Architects**: the **Tower of Rankings**. The tower preserves the order of all things—wealth, honour, rarity, and renown.

You are **SQLaus**, a tired but determined data explorer who has finally reached the tower’s entrance. Rescue lies at the top, but the path is blocked by five sealed floors. Each floor opens only for those who can answer a ranking question in SQL.

The islanders joke that only a true **Guardian of the Data** can reactivate the ancient beacon at the summit. To do that, you must master five powers:

1. `LIMIT` – retrieve only the rows you need
2. `FETCH FIRST` – the standard form of row limiting
3. `ROW_NUMBER()` – give every row a unique position
4. `RANK()` – honour ties and leave gaps afterwards
5. `DENSE_RANK()` – honour ties without leaving gaps

---

## Challenge 1 – The Gate of the Worthy

**Story**  
A stone tablet glows at the tower gate:

> Only the three richest inhabitants may enter the tower.

The gatekeeper claims that brute force is for barbarians; real heroes sort first and limit wisely.

**Task**  
Return the three inhabitants with the highest amount of gold.

**Expected output columns**  
`name`, `gold`

**Hint**  
Sort from highest gold to lowest, then keep only the first three rows.

**Reference solution**
```sql
SELECT name, gold
FROM inhabitant
ORDER BY gold DESC, name ASC
LIMIT 3;
```

**What this teaches**  
Top-N queries need both an ordering criterion and a row limit.

---

## Challenge 2 – The Hall of Formal Scrolls

**Story**  
Behind the first gate, ancient scrolls reject informal magic. The guardian speaks in ceremonial SQL:

> Show the same three richest inhabitants again, but use the standard wording of the old architects.

A nearby islander whispers: “Same mission, different spell.”

**Task**  
Return the same three richest inhabitants, but solve it with standard row-limiting syntax.

**Expected output columns**  
`name`, `gold`

**Hint**  
Use `FETCH FIRST ... ROWS ONLY` after the ordering.

**Reference solution**
```sql
SELECT name, gold
FROM inhabitant
ORDER BY gold DESC, name ASC
FETCH FIRST 3 ROWS ONLY;
```

**What this teaches**  
`LIMIT` and `FETCH FIRST` express the same idea in different syntax styles.

---

## Challenge 3 – The Stair of Single Crowns

**Story**  
The next floor contains a glowing leaderboard. Every inhabitant must receive a unique place in line.

> Assign a unique position to every inhabitant according to their gold, from richest to poorest.

The tower warns you that equal wealth does not automatically mean equal rank.

**Task**  
Show all inhabitants with their gold and a unique position number.

**Expected output columns**  
`name`, `gold`, `row_num`

**Hint**  
Use a window function with `OVER (ORDER BY ...)`.

**Reference solution**
```sql
SELECT name,
       gold,
       ROW_NUMBER() OVER (ORDER BY gold DESC, name ASC) AS row_num
FROM inhabitant
ORDER BY row_num;
```

**What this teaches**  
`ROW_NUMBER()` creates an explicit leaderboard with a unique position for every row.

---

## Challenge 4 – The Chamber of Shared Glory

**Story**  
The tower changes its rules. In this chamber, equal achievement must receive equal honour.

> Rank the inhabitants by reputation. Equal reputation must receive the same rank, and the next rank must show the gap.

The chamber values fairness over simplicity.

**Task**  
Rank all inhabitants by reputation. Ties must share the same rank, and later ranks must skip numbers after a tie.

**Expected output columns**  
`name`, `reputation`, `rank_num`

**Hint**  
Use `RANK()`.

**Reference solution**
```sql
SELECT name,
       reputation,
       RANK() OVER (ORDER BY reputation DESC) AS rank_num
FROM inhabitant
ORDER BY rank_num, name;
```

**What this teaches**  
`RANK()` handles ties by assigning the same rank and leaving a gap afterwards.

---

## Challenge 5 – The Summit of Equal Honour

**Story**  
At the summit, the last guardian rejects gaps in the ranking.

> Rank the villages by population. Ties must share the same rank, but the numbering must remain consecutive.

One final ordered answer will reactivate the beacon.

**Task**  
Rank all villages by population so that ties share a rank and no rank numbers are skipped.

**Expected output columns**  
`name`, `population`, `dense_rank_num`

**Hint**  
Use `DENSE_RANK()`.

**Reference solution**
```sql
SELECT name,
       population,
       DENSE_RANK() OVER (ORDER BY population DESC) AS dense_rank_num
FROM village
ORDER BY dense_rank_num, name;
```

**What this teaches**  
`DENSE_RANK()` handles ties without leaving gaps in the numbering.

---

## Bonus Challenge – The Balcony of Local Champions

**Story**  
As the beacon warms up, a final console lights up on the balcony:

> For each village, show the two richest inhabitants.

Now the tower no longer wants one global ranking. It wants a ranking inside each local community.

**Task**  
For each village, return the two inhabitants with the highest gold.

**Expected output columns**  
`village_name`, `inhabitant_name`, `gold`

**Hint**  
Create a row number per village and keep only rows 1 and 2.

**Reference solution**
```sql
SELECT village_name, inhabitant_name, gold
FROM (
    SELECT v.name AS village_name,
           i.name AS inhabitant_name,
           i.gold,
           ROW_NUMBER() OVER (
               PARTITION BY v.id
               ORDER BY i.gold DESC, i.name ASC
           ) AS row_num
    FROM inhabitant i
    JOIN village v
      ON v.id = i.village_id
) ranked
WHERE row_num <= 2
ORDER BY village_name, gold DESC, inhabitant_name;
```

**What this teaches**  
Many real ranking questions are local to a group, not global across a full dataset.

---

## Suggested closing text
The beacon at the summit lights up. The Tower of Rankings recognises you as a true **Guardian of the Data**. You have mastered row limiting, formal Top-N syntax, unique row numbering, tie-aware ranking, and dense ranking without gaps. Rescue is finally within reach—but deeper SQL mysteries still await beyond the tower.

---

## Short illustration prompts you can use
1. **Cover illustration**  
   “A mysterious tropical SQL island in a digital ocean, with a tall ancient stone tower glowing with blue ranking symbols, retro cosmic adventure mood, original game art, no copyrighted characters, playful and academic.”

2. **Gate of the Worthy**  
   “A stone gate on a fantasy data island, golden leaderboard tablet glowing with the number 3, treasure and database symbols, original retro space-adventure style, no copyrighted characters.”

3. **Summit scene**  
   “An ancient signal beacon at the top of a ranking tower on SQL Island, glowing query runes, stars above a digital ocean, original cosmic adventure illustration, no copyrighted characters.”
