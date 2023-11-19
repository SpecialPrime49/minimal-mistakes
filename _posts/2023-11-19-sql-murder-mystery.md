## Solving the SQL Murder Mystery Challenge by KnightLab
![dashboard](/assets/images/174092-clue-illustration.png "SQL Murder Mystery Challenge by knight lab")

The [Murder Mystery Challenge](https://mystery.knightlab.com/) by knight lab is designed to practice profiling and understanding a relatively simple data model. The goal of the challenge is to solve a fictional crime. I was able to determine the culprit with the following queries:


```sql
--Profile DB
select T.[name]as 'Table_Name',
C.[Name] as 'Column_Name'
from sys.objects T
join sys.columns C
on T.object_ID = C.object_ID
where T.[type_desc] like 'USER_TABLE'

select [name] from sys.objects where [type_desc]='USER_TABLE'


--Profile crime reports with murder type
select * from crime_scene_report where [type]='murder' and [date]='20180115'

select distinct [type] from crime_scene_report

select count(*) from crime_scene_report where [type]='murder'

select count(distinct[fileDate]) as 'murder_report_count' from crime_scene_report where [type]='murder'

select count(*) 'report count', city from crime_scene_report where [type]='murder' group by city order by [report count] DESC
select * from crime_scene_report where [type]='murder' and [date]='20180115'
--Fact1: based on the [description] field the murder reported on 2018-01-15 seems to be the most reliable report  it occured in SQL City. Provided Description: Security footage shows that there were 2 witnesses. The first witness lives at the last house on "Northwestern Dr". The second witness, named Annabel, lives somewhere on "Franklin Ave".


--Check individuals who have gym memberships and checked in to gym on date 
select * from person

select * from get_fit_now_member

select P.name as 'P.name',G.Name as 'GymMember_name', C.membership_id,C.Check_in_date,C.Check_in_time,C.Check_out_time /*into #temp*/ from get_fit_now_member G 
inner join person P
on cast(P.id as nvarchar) =cast(G.person_id as nvarchar)
inner join get_fit_now_check_in C
on C.membership_id = G.id
where C.Check_in_date=20180115
--FACT2:Armando Huie, Taylor Skyes,Edgar Bamba,Joline Hollering also checked in on the date of the murder


--Check for details of witnesses 
select * from person where [address_street_name] like 'Northwestern Dr' OR [address_street_name] like 'Franklin%' and [name] like 'Annabel%'
--FACT3 Annabell Miller (id=16371) is one witness

--See Annabell's interview details 
select * from interview where person_id='16371'
--FACT4: "I saw the murder happen, and I recognized the killer from my gym when I was working out last week on January the 9th."


--Confirm Annabell checked into gym at 2018-01-09 
select P.name as 'P.name',G.Name as 'GymMember_name', C.membership_id,C.Check_in_date,C.Check_in_time,C.Check_out_time /*into #temp*/ from get_fit_now_member G 
inner join person P
on cast(P.id as nvarchar) =cast(G.person_id as nvarchar)
inner join get_fit_now_check_in C
on C.membership_id = G.id
where C.Check_in_date=20180109
--FACT4 Annabell did check into  gym:	"Annabel Miller	90081	20180109	1600	1700"

--FACT5:Names in Gym on 2018-01-09 
/*
Adriane Pelligra
Shondra Ledlow
--Annabel Miller
Zackary Cabotage
Joe Germuska
Blossom Crescenzo
Sarita Bartosh
Jeremy Bowers
Burton Grippe
Carmen Dimick*/


--Check interview from any of the names that were at the gym when Annabell claimed she recognized
select * from person where name in (
'Adriane Pelligra',
'Shondra Ledlow',
'Zackary Cabotage',
'Joe Germuska',
'Blossom Crescenzo',
'Sarita Bartosh',
'Jeremy Bowers',
'Burton Grippe',
'Carmen Dimick')

select * from interview where person_id in(
10815,
15247,
28073,
28819,
31523,
55662,
67318,
83186,
92736
) 

select * from person where id =67318

select * from interview where id =67318
--FACT6: Potential confession from Jeremy Bowers:
/* "I was hired by a woman with a lot of money. I don't know her name but I know she's around 5'5" (65") or 5'7" (67"). She has red hair and she drives a Tesla Model S. I know that she attended the SQL Symphony Concert 3 times in December 2017."*/


--Corroborate description via the drivers_license table
select DL.id as 'Lic#',DL.height, DL.car_make,DL.car_model, P.name,P.id as 'PersonID', P.SSN, I.annual_income from drivers_license DL 
inner join person P
on DL.id=P.license_id
inner join income I
on P.SSN=I.ssn
where height >= 65 and height<='67' and gender='female' and car_make = 'Tesla' 
--FACT7: Two women fit the height, wealth, and car ownership description: Red Korb &Miranda Priestly 


--Check Facebok Checkins
Select * from facebook_event_checkin F 
inner join person P
on P.id=F.person_id
where person_id in (78881
,99716)
--FACT8: Miranda Priestly did attend the SQL Symphony in 2017

--Conclusion: Killer: Miranda Priestly
```