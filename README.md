SELECT * FROM d1;
select TOP 3 State,avg(Growth)*100 avg_growth from d1 group by State order by avg_growth desc;

select State,round(avg(Sex_Ratio),0) avg_sex_ratio from d1 group by state order by avg_sex_ratio desc

select TOP 3 State,avg(Growth)*100 avg_growth from d1 group by State order by avg_growth ASC;

select TOP 3 State,round(avg(Sex_Ratio),0) avg_sex_ratio from d1 group by state order by avg_sex_ratio ASC;

drop table if exists #topstates;
Create table #Topstates
( State nvarchar(50),
  #Topstate float

  )

insert into #Topstates
select TOP 5 state,round(avg(Literacy),0) avg_litercy from d1
group by State order by avg_litercy desc;
select * from #Topstates;

drop table if exists #Bottamstates;
Create table #Bottamstates
( State nvarchar(50),
  #Bottamstate float

  )

insert into #Bottamstates
select TOP 5 state,round(avg(Literacy),0) avg_litercy from d1
group by State order by avg_litercy asc;
select * from #Bottamstates;
--join table
SELECT * FROM d1;
SELECT * FROM d2;

SELECT District FROM d1 INNER JOIN d2 ON d1.District.d2.District;

SELECT *
FROM d1
INNER JOIN d2
ON d1.District = d2.District;


SELECT *
FROM d1
INNER JOIN d2
ON d1.District = d2.District;

SELECT a.District,a.State,a.Sex_Ratio,b.Population FROM d1 a INNER JOIN d2 b ON a.District=b.District;
-- Male and Female
SELECT d.State, SUM(d.male) Total_males, SUM(d.female) Total_females FROM
(SELECT c.District,c.State State,Round(c.Population/(c.Sex_Ratio+1),0) male, Round((c.Population*c.Sex_Ratio)/(c.Sex_Ratio+1),0) female FROM
(SELECT a.District,a.State,a.Sex_Ratio/100 Sex_Ratio ,b.Population FROM d1 a INNER JOIN d2 b ON a.District=b.District) c) d
GROUP BY d.State;

-- Literacy

SELECT c.State,SUM(Literacy_People) Total_literacy_People,SUM(iliteracy_People) Total_iliteracy_People FROM
(SELECT d.District,d.State,ROUND(d.Literacy_Ratio*d.Population,0) Literacy_People,ROUND((1-d.Literacy_Ratio)*d.Population,0) iliteracy_People FROM
(SELECT a.District,a.State,a.Literacy/100 Literacy_Ratio ,b.Population FROM d1 a INNER JOIN d2 b ON a.District=b.District) d) c
GROUP BY c.State

--GROWTH

SELECT g.total_area/g.previous_census_population,g.total_area/g.current_census_population FROM
(SELECT q.*,r.Total_area FROM
(SELECT '1' as keyy,n.*FROM
(SELECT SUM (m.previous_census_population) previous_census_population,SUM(m.current_census_population) current_census_population FROM
(SELECT e.State,SUM(e.previous_census_population) Previous_census_population,SUM(e.current_census_population) Current_census_population FROM
(SELECT d.District,d.State,ROUND(d.Population/(1+d.Growth_Ratio),0) previous_census_population,d.Population current_census_population from
(SELECT a.District,a.State,a.Growth Growth_Ratio,b.Population FROM d1 a INNER JOIN d2 b ON a.District=b.District) d)e
GROUP BY e.State)m)n)q Inner JOIN(


SELECT '1' as keyy,z.*FROM(
SELECT SUM(Area_km2) Total_area FROM d2)z) r on q.keyy=r.keyy)g;

--window

OUTPUT TOP 3 districts FROM each STATE WITH highest Literacy rate

SELECT a.* FROM
(SELECT District,State, Literacy,RANK() OVER(partition by State ORDER BY Literacy desc) rnk FROM d1) a
WHERE a.rnk in (1,2,3) ORDER BY State;

select a.* from
(select district,state,literacy,rank() over(partition by state order by literacy desc) rnk from d1) a

where a.rnk in (1,2,3) order by state
