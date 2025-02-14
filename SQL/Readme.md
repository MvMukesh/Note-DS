# Descriptive Analysis of FIFA 19 Player 
Dealing with subset of FIFA 19 players dataset
---
## Steps Involved
* `Data Eyeballing` => Checking data manually say in Excell
* `Data Dictionary` => Makes us understand Content, Structure, Format of data
* `Question's` => Key questions nead to be answered 
* `Analyzing Data and Creating Table Structure` => Create table structure for data to put in SQL
* `Loading data to MYSQL table`
* `Data Analysis`
  * `Simple Queries`
  * `Advanced Queries`
---
* `Data Eyeballing`: Open in Excell if data is large use shell command like `head,tail,cut` to take sample of data
 ![image](https://user-images.githubusercontent.com/26667491/126902213-96b80ec0-8777-4e64-a851-92ad6e298b64.png)
  * Data shape => (15,16644)
  * Put data filter to get more about every column
  * Column after column eyeballing => to understand data type
* `Data Dictionary`: Describes what each variable means in dataset
  * `ID [int(11)]`: unique identification number for player
  * `Name [varchar(255)]`, `Age [int(2)]`, `Nationality [varchar(255)]`
  * `Overall [int(11)]`: overall rating of player
  * `Potential [int(11)]`: potential rating of player
  * `Club [varchar(255)]`
  * `Value [int(11)]`: Current value of player in Euroes
  * `Wage [int(11)]`, `Preferred.Foot [enum('Left','Right')]`, `Jersey.Number [int(11)]`, `Joined [date]`, `Height [varchar(10)]`
  * `Weight [int(3)]`, `Penalties [int(3)]`
* `Question's`: Important is Asking right questiong and getting answer
  * Q1 => Total number of player?
  * Q2 => Nationality distribution?
  * Q3 => Total wage of player? Wage Distribution, average, stdev, highest/lowes individual
  * Q4 => Rating overall best/worst ?
  * Q5 => Club best/worst, top5, bottom5, can be obtained by observing ratings of player related with Club as particular Club rating card in not in dataset
  * Q6 => Preferred.Foot with rating , find which foot player playes good?, diffrent Foot distribution?
  * Q7 => Jersey.Number with rating , Luck factor associated with jersey or Value or Penalties or rating ? it is hard to define luck
  * Q8 => Club name starting with any lucky Letter
  * Q9 => Palyer joining respective Clubs in a particular timeline or date?
  * Q10 => Player joining there Clubs datewise? On each date how many player joined club?
  * Q11 => player joining Club Yearly?........... and many more other possible question's
---
Application Part
---
* `Analyzing Data and Creating Table Structure`: 
  * Create database name_of_db;
  * use name_of_db;
  * create table players( ID int,  name varchar(255) , age int(3) , nationality varchar(255) , overallrating int , potentialrating int,  club varchar(255),  value int,  wage int,  preferredfoot ENUM(“Left”, ”Right”) , jerseynumber int , joined datetime,  height varchar(10),   weight int(3),  penalties int(3) );
  * desc players;
* `Loading data to MYSQL table`: from csv file
  * load data local infile "csv_file_path" into table players columns terminated by "," optionally enclosed by "'" ignore 1 lines;
    * Always have a firm luck a Warning, to make sure all data types are correct and etc....
  * warnings
    * To import date it must be formates as `YYY-MM-DD` to avoide fute errors as MYsql cant load lond dates i.e June 23 2800
  * select * from players limit 10\G;  => row wise data retrival
* `Data Analysis (Simple Queries) Question's`:
  * Total number of players in dataset?
    * select count(*) from players;
  * Different Nationality in dataset?
    * select count(distinct nationality) as number_of_nationalities from players;
  * Total wages given to playesr with avg,std?
    * select sum(wage) as total_wage, avg(wage) as avg_wage, stddev(wage) as stddev_wage from players;
  * Highest number of player's by Nationality, top_3 nationality and there player count (Frequncy Distribution of Nationality)
    * select count(*) as freq, nationality from players group by nationality;  (players_freq | nationality)
    * select count(*) as freq, nationality from players group by nationality order by freq desc limit 3;
  * Highest/lowest/overall Wage in dataset and which player associated with it?
    * select max(wage) from players; 
    * select Name from players where wage = max(wage) value;  => can be solved using `subquary`
    * select Name from players where wage = (select max(wage) from players);
    * select min(wage) from players;
    * select Name from players where wage = (select min(wage) from players);
    * select Name from players where overallrating = (select max(overallrating) from players);
  * Top(highest total/ highest avg. ratins) Clubs based on there player ratings?
    * select sum(overallrating) as total_rating, club from players group by club order by total_rating desc;
    * select sum(overallrating) as total_rating, club from players group by club order by total_rating desc limit 3;
    * select avg(overallrating) as avg_rating, club from players group by club order by avg_rating desc;
    * select avg(overallrating) as avg_rating, club from players group by club order by avg_rating desc limit 3;
* `Data Analysis (Advanced Queries) Question's`:
  * What is Player prefered foot distribution?
    * select count(*) as freq, preferredfoot from players group by 2 order by 1 desc;  => 2 & 1 r numeric position of variables preferredfoot & freq
  * Luckey JerseyNumber? (defining asumption for luckey as this is subjective thing here => for highest total wage)
    * select sum(wage) as total_wage, jerseynumber from players group by jerseynumber;  
    * select sum(wage) as total_wage, jerseynumber from players group by jerseynumber order by total_wage desc limit 1;
  * Frequeny distribution of nationalities among players whose Club name starts with M?
    * select count(*) as freq, nationality from players where club like "M%" group by nationality;
  * `Total number of players who have joined respective club in date range of 2018-05-20 to 2019-04-10 (both inclusive i.e add = sign)?` `Most important filtering`
    * select count(*) from players where joined >= '2018-05-20' and joined < '2019-04-10';
  * `Total number of players joining there respective club date wise?` `Most important filtering`
    * select count(*) as freq, joined from players group by joined; it shows some zeroes which look like ![image](https://user-images.githubusercontent.com/26667491/126998828-faadbb90-6070-4acd-8b4d-83cc0203bf47.png)
    * select count(*) as freq, date (joined) from players group by joined;
      * `Notice:` use of date function as date are stored as time stemp
      *  grouping can be done also by joined
    * select count(*) as freq, date (joined) from players group by date (joined);
  * `Total number of players joining there respective club year wise?` `Most important filtering`
    * select count(*) as freq, year (joined) from players group by year (joined);   year function takes particular date cinvert it into year
 
`NOTES:`
* Indexing helps data retrival very fast
* Indexing cannot be done on all column as increases space complexity
