SELECT [ALL|DISTINCT] 
{ * | expr [[AS] c_alias] { , expr [[AS] c_alias] ... } }
FROM  tableref { , tableref ... }
[ WHERE  search_condition ][ GROUP BY colname { , colname ... } [HAVING group_search_condition ] ]
| subquery UNION [ALL] subquery



1. 查询销售量最高的产品的前两名（pname，volume） 
  SELECT pname, volume
  FROM Production natural join
  (SELECT pid, sum(CAST(volume AS SIGNED))  AS volume
  FROM Deal
  GROUP BY pid
  ORDER BY volume DESC LIMIT 2) AS top_two;

2. 查询2017年生产的产品的总销量（pname，volume）

  SELECT pname, sum(CAST(volume AS SIGNED)) AS volume
  FROM Deal natural join
  (SELECT pid, pname
  FROM Product
  WHERE Year(pdate) = 2017) AS target_p
  GROUP BY pname;

3. 查询产品编号为2且销售量超过100的销售人员的姓名及所在的公司（sname，aname） 

   SELECT sname, aname
   FROM Sales natural join Agent natural join
   (SELECT sid
   FROM Deal
   WHERE CAST(pid AS SIGNED)=2 and CAST(volume AS SIGNED)>100) AS target_s;

4. 查询所有代理商所有产品的销量（aname，pname，volume） 

   SELECT aname，pname，sum(CAST(volume AS SIGNED)) AS volume
   FROM Deal natural join Production natural join Sales natural join Agent
   GROUP BY aname, pname;

5. 查询每个产品有多少个销售人员在销售（pname，scount（计数））

   SELECT pname, scount
   FROM Production natural join
   (SELECT pid, count(sid)  AS scount
   FROM Deal
   GROUP BY pid) AS count_s;

6. 查询名称包含BBB的代理商中的所有销售人员（aname，sname）

   SELECT aname, sname
   FROM Sales natural join
   (SELECT *
   FROM Agent
   WHERE aname like '%BBB%') AS target_a;

7. 查询总销量最差的产品（pname，volume） 
   SELECT pname, volume
   FROM Production natural join
   (SELECT pid, sum(CAST(volume AS SIGNED))  AS volume
   FROM Deal
   GROUP BY pid
   ORDER BY volume ASC LIMIT 1) AS worst;

8. 查询每种产品销售总量最高的销售人员（pname，sname，volumn） 
   SELECT pname, sname, volumn
   FROM
   (SELECT d.pid, d.sid, max_pv.volumn AS volumn
   FROM Deal d,
   (SELECT pid, max(CAST(volume AS SIGNED)) AS volumn
   FROM Deal
   GROUP BY pid) AS max_pv
   WHERE d.pid=max_pv.pid and CAST(d.volume AS SIGNED)=max_pv.volumn) AS max_s
   natural join Production natural join Sales;

