# BeatFantasy-
SQL repository for internship of Beat fantasy
to import the csv
  -open a new schema and name the database
  -on the subpart of table of the database right click on the table option and click 'import from table'
  -mention the file location path and continue in the wizard
to import pdf
  -follow same steps for importing a csv but first convert the pdf to csv from https://www.ilovepdf.com/pdf_to_excel and save this into google sheets and download into system as a csv


ANSWERS TO QUESTIONS REGARDING DATA SET 

1.What were the top 10 user teams in the match and what were their respective ranks?
  ans- 1. 007 M S D (1)
       2. 007 M S D (2)
       3. 007 M S D (3)
       4. 007 M S D (4)
       5. 007 M S D (5)
       6. 007 M S D (6)
       7. 007 M S D (7)
       8. 007 M S D (8)
       9. 007 M S D (9)
       10. 007 M S D (10)


2.What was the average number of teams created by a user?
  ans-1.0166

code-
  WITH user_team_counts AS (
    SELECT `User (Team)`, COUNT(*) AS team_count
    FROM beatfantasy.user_teams
    GROUP BY `User (Team)`
)
SELECT AVG(team_count) AS average_teams_per_user
FROM user_team_counts;


3.What percentage of users created 1 team, 2 teams, 3 teams, ..., up to 20 teams?
  ans- 99.97408% of users created 1 team.
     - 0.02592% of users created 65 teams

  code-
WITH user_team_counts AS (
    SELECT 
        `User (Team)`, 
        COUNT(*) AS team_count
    FROM beatfantasy.user_teams
    GROUP BY `User (Team)`
),


total_users AS (
    SELECT 
        COUNT(*) AS total_count 
    FROM user_team_counts
)


SELECT 
    team_count,
    COUNT(*) AS user_count,
    (COUNT(*) * 100.0 / (SELECT total_count FROM total_users)) AS percentage
FROM user_team_counts
GROUP BY team_count
ORDER BY team_count;


4.What percentage of teams had captains by positions - GK, DEF, MID, ST?
  ans-71.11% of teams had captains who played in the MID (Midfield) position.
      16.68% of teams had captains who played in the DEF (Defense) position.
      11.63% of teams had captains who played in the ST (Striker) position.
      0.59% of teams had captains who played in the GK (Goalkeeper) position.     

code-
  WITH total_teams AS (
    SELECT COUNT(*) AS total_count FROM beatfantasy.user_teams
),
captain_positions AS (
    SELECT 
        `User (Team)`,
        CASE 
            WHEN `Player 1 (Captain)` IN ('Yvon Mvogo') THEN 'GK'
            WHEN `Player 1 (Captain)` IN ('Phillipp Mwene', 'Armando Obispo', 'Olivier Boscagli', 'Pantelis Chatzidiakos', 'Bruno Martins Indi', 'Jordan Teze', 'Owen Wijndal') THEN 'DEF'
            WHEN `Player 1 (Captain)` IN ('Cody Gakpo', 'Ibrahim Sangare', 'Jesper Karlsson', 'Mario Gotze', 'Dani De Wit', 'Joey Veerman', 'Fredrik Midtsjo', 'Jordy Clasie', 'Hakon Evjen') THEN 'MID'
            ELSE 'ST'
        END AS position
    FROM beatfantasy.user_teams
)
SELECT 
    position,
    COUNT(*) AS position_count,
    (COUNT(*) * 100.0 / (SELECT total_count FROM total_teams)) AS percentage
FROM captain_positions
GROUP BY position;


5.What percentage of teams had vice captains by team - ALK, PSV
  ans-69.38% of teams had vice captains from the team PSV.
      30.62% of teams had vice captains from the team ALK.

code-
  WITH total_teams AS (
    SELECT COUNT(*) AS total_count FROM beatfantasy.user_teams
),
vice_captain_teams AS (
    SELECT 
        CASE 
            WHEN `Player 2 (Vice Captain)` IN ('Cody Gakpo', 'Ibrahim Sangare', 'Joey Veerman', 'Mario Gotze', 'Bruno Martins Indi', 'Fredrik Midtsjo', 'Olivier Boscagli', 'Phillipp Mwene', 'Ritsu Doan') THEN 'PSV'
            ELSE 'ALK'
        END AS team
    FROM beatfantasy.user_teams
)
SELECT 
    team,
    COUNT(*) AS team_count,
    (COUNT(*) * 100.0 / (SELECT total_count FROM total_teams)) AS percentage
FROM vice_captain_teams
GROUP BY team;
