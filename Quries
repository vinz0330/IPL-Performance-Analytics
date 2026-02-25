
-- Identify underrated players who maintained a high batting average despite
-- limited match opportunities?

SELECT season,fullname,current_innings,
SUM(runs) AS total_runs,
COUNT(match_id) AS matches_played,
(SUM(runs)/COUNT(match_id)) as batting_avg
FROM Batting
GROUP BY season,fullname,current_innings
HAVING COUNT(match_id) between 3 and 5
AND (SUM(runs)/COUNT(match_id)) >= 30
ORDER BY season, batting_avg DESC;
use ipl;

-- Which bowlers consistently maintained an excellent economy rate across a
-- season while bowling a significant number of overs?

select season,fullname,bowling_team,
sum(conceded) as conceded,sum(overs) as overs,
sum(conceded)/sum(overs) as economy
from bowling group by season, fullname, bowling_team
having sum(overs) > 10 and economy < 6 order by economy ;

-- Who are the top 10 wicket-takers in IPL history?

SELECT fullname,SUM(wickets) AS total_wickets
FROM bowling
GROUP BY fullname
ORDER BY total_wickets DESC limit 10 ;

-- Who was the highest wicket-taker in each IPL season? 

WITH season_wickets AS (SELECT season,fullname,SUM(wickets) AS total_wickets,
RANK() OVER (PARTITION BY season ORDER BY SUM(wickets) DESC) AS rnk
FROM bowling 
GROUP BY season, fullname)
SELECT season,fullname,total_wickets FROM season_wickets
WHERE rnk = 1 ORDER BY season;

-- Who has scored the most runs in IPL history?

SELECT fullname,SUM(runs) AS total_runs
FROM batting
GROUP BY fullname
ORDER BY total_runs DESC
LIMIT 1;

-- Who scored the highest number of runs in each IPL season?

WITH ranked_batters AS 
(SELECT season,fullname,SUM(runs) AS total_runs,
ROW_NUMBER() OVER (PARTITION BY season ORDER BY SUM(runs) DESC) AS rnk 
FROM batting
GROUP BY season, fullname)
SELECT season,fullname,total_runs
FROM ranked_batters
WHERE rnk = 1
AND season BETWEEN 2008 AND 2023
ORDER BY season;

-- Which bowler was the most economical in each IPL season among those who
-- bowled a significant number of overs?

WITH season_economy AS (
SELECT season,fullname,SUM(conceded) AS conceded,SUM(overs) AS overs,SUM(conceded)/SUM(overs) AS economy,
RANK() OVER (PARTITION BY season ORDER BY SUM(conceded)/SUM(overs) ASC) AS rnk 
FROM bowling GROUP BY season, fullname HAVING SUM(overs) > 50 )
SELECT season, fullname, economy FROM season_economy WHERE rnk = 1 ORDER BY season;

-- Which bowlers maintained the best economy rate in each season?

select season, fullname, sum(conceded) as conceded,
sum(overs) as overs,sum(conceded)/sum(overs) as economy 
from bowling
group by season, fullname
having sum(overs) > 10 and economy < 6 order by economy;

-- Which are the top 10 batsmen across all IPL seasons who have scored at
-- least 2500 runs while maintaining a strike rate of 135 or higher?

SELECT fullName, SUM(runs) AS total_runs, SUM(ballsFaced) AS total_balls, 
(SUM(runs)/SUM(ballsFaced))*100 AS strike_rate
FROM batting GROUP BY fullName
HAVING SUM(runs) >= 2500 AND (SUM(runs)/SUM(ballsFaced))*100 >= 135
ORDER BY strike_rate DESC, total_runs DESC LIMIT 10;

-- Which batsman recorded the highest strike rate in each IPL season while
-- scoring at least 500 runs?

WITH season_stats AS 
(SELECT season, fullName, SUM(runs) AS total_runs, SUM(ballsFaced) AS total_balls, (SUM(runs)/SUM(ballsFaced))*100 AS strike_rate 
FROM batting GROUP BY season, fullName HAVING SUM(runs) >= 500),
ranked AS (SELECT *, ROW_NUMBER() OVER(PARTITION BY season ORDER BY strike_rate DESC) AS rn FROM season_stats)
SELECT season, fullName, total_runs, total_balls, strike_rate FROM ranked WHERE rn = 1 ORDER BY season;

#Which franchise has been the most successful and most consistent in IPL history, based on total wins?

SELECT name,SUM(matcheswon) AS total_wins,sum(matchesplayed) as matches_played,
SUM(matcheswon)/sum(matchesplayed)*100 as Winning_Percentage
FROM points_table GROUP BY name ORDER BY total_wins DESC;

-- Which was the worst-performing season for championship-winning teams
-- (Mumbai Indians and Chennai Super Kings) based on lowest points and
-- wins?

WITH ranked AS
(SELECT season, `name`, matchesplayed,matcheswon, matchpoints, 
ROW_NUMBER() OVER(PARTITION BY `name` ORDER BY matchpoints ASC, matcheswon ASC) AS rn 
FROM points_table WHERE `name` IN ('Mumbai Indians','Chennai Super Kings'))
SELECT season, `name`,matchesplayed, matcheswon, matchpoints
FROM ranked WHERE rn = 1;

-- Which team has recorded the highest number of match wins across all IPL
-- seasons?

SELECT `name`,sum(matchesplayed) as matches_played , SUM(matcheswon) AS total_wins
FROM points_table
GROUP BY `name`
ORDER BY total_wins DESC;

-- Which teams have the highest success rate while chasing targets in IPL
-- matches, considering teams with significant match experience?

WITH chase AS
 (SELECT winner, (winner = home_team AND home_score = 2nd_inning_score) OR 
 (winner = away_team AND away_score = 2nd_inning_score) AS chased_successfully 
 FROM summary)
 SELECT winner AS team, SUM(chased_successfully) AS chase_wins, COUNT(*) AS total_matches,
 SUM(chased_successfully)/COUNT(*)*100 AS chase_win_percentage 
 FROM chase GROUP BY team having total_matches >45 ORDER BY chase_win_percentage DESC;
 
-- Which teams have been most successful at defending targets in IPL matches,
-- considering teams with significant match experience?
 
 With defend as 
 (select winner,(winner = home_team AND home_score = 1st_inning_score) OR 
 (winner = away_team AND away_score = 1st_inning_score) AS defended_successfully 
 FROM summary)
 select winner as team,SUM(defended_successfully) AS defend_wins, COUNT(*) AS total_matches,
 sum(defended_successfully)/COUNT(*)*100 as Defend_win_perc
 from defend group by team having total_matches >45 order by Defend_win_perc Desc;

-- Which innings has the highest overall team strike rate across IPL matches,
-- considering a significant number of balls faced?
 
SELECT current_innings,SUM(runs) AS total_runs,SUM(ballsFaced) AS total_balls,
(SUM(runs) / SUM(ballsFaced)) * 100 AS team_strike_rate
FROM batting GROUP BY current_innings HAVING SUM(ballsFaced) > 20000 ORDER BY team_strike_rate DESC;

-- How dominant has Mumbai Indians been against Royal Challengers
-- Bangalore in head-to-head IPL matches?

SELECT 'MI vs RCB' AS matchup,SUM(winner = 'MI') AS MI_wins,
SUM(winner = 'RCB') AS RCB_wins,COUNT(*) AS total_matches
FROM summary WHERE (short_name = 'MI v RCB' OR short_name = 'RCB v MI')
AND ((home_team = 'MI' AND away_team = 'RCB')OR (home_team = 'RCB' AND away_team = 'MI'));

-- Which teams recorded the largest margins of victory by runs across IPL
-- seasons?

select season,short_name,win_by_runs from summary order by win_by_runs desc;

-- Which teams recorded the largest margins of victory by wickets across IPL
-- seasons?

select season,short_name,win_by_wickets from summary order by win_by_wickets desc;
