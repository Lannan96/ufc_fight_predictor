# Problem Statement
The purpose of this project is to produce a model to predict winners of UFC fights. The comparison of this models performance will be judge against prediction I make myself based on my knowledge of the sport and later compared to the bookies favourite pick.

As we have access to a rich dataset there are numerous ways we can make prediction of the outcome of the fight. Listed below are the technique we will try and compare to each other.
    * Comparing fighters based career stats
    * Using career stats again, but calculating them so they are accurate to the time (I.E excluding fights that are in the future of the fight currently being predicted)
    * Predicting the stats of a fight and using these prediction to predict the winner

# Step 1) Data Exploration
The first step of any project is understanding our data, this is done by...
Whilst doing this I will keep two question in mind to help me later, these are, how will I clean this data and what features could I engineer.

First things first lets use information from the scraping to explain our database structure and the columns in the data.

## Database schema
Below we can see our database schema made up of three tables: 1) Fighter, 2) Fight, and 3) Event.
As we can see from the schema the 'fight' table links to 'fighter' and 'event' as all of our data follows the following format -> An event is made up of fights -> a fight contains two fighter.

![Database](ufc_database.png)

## Table information
Next we will outline what each of the columns in our tables represent and explain some technical definition to help understand our columns.

#### Fighter Table
The example data comes from the fighter found at the following URL - http://ufcstats.com/fighter-details/e1248941344b3288
<br>
<br>

| Column Name | Description | Format | Example |
| --- | --- | --- | --- |
| fighter_id | Primary key of the fighter formed from the last part of the URL. 'http://ufcstats.com/fighter-details/e1248941344b3288' the fighter_id would be 'e1248941344b3288' | varchar(255) PK | 'e1248941344b3288' |
| name | Name | varchar(255) | 'Alexander Volkanovski' |
| height | Height in Inches | int | '66' |
| weight | Weight in IBS | int | '145' |
| reach | Reach in Inches | int | '71' |
| stance | Stance | varchar(255) | 'Orthodox' |
| dob | DOB | date | '1988-09-29' |
| SLpM | Significant Strikes Landed per Minute | float | '6.19' |
| str_acc | Significant Striking Accuracy | float | '57' |
| SApM | Significant Strikes Absorbed per Minute | float | '3.42' |
| str_def | Significant Strike Defence (the % of opponents strikes that did not land) | float | '58' |
| TD_avg | Average Takedowns Landed per 15 minutes | float | '1.84' |
| TD_acc | Takedown Accuracy | float | '37' |
| TD_def | Takedown Defense (the % of opponents TD attempts that did not land) | float | '70' |
| sub_avg | Average Submissions Attempted per 15 minutes | float | '0.2' |



#### Fight Table
The example data comes from the fight found at the following URL - http://ufcstats.com/fight-details/bec3154a11df3299
<br>
<br>

| Column Name | Description | Format | Example |
| --- | --- | --- | --- |
| fight_id | Primary key of the fight formed from the last part of the URL. 'http://ufcstats.com/fight-details/bec3154a11df3299' the fight_id would be 'bec3154a11df3299'  | varchar(255) PK | 'bec3154a11df3299' |
| fighter_a_id_FK | Fighter_id for the red corner of the fight. Foreign Key from fighter table | varchar(255) FK | 'e1248941344b3288' |
| fighter_b_id_FK | Fighter_id for the blue corner of the fight. Foreign Key from fighter table | varchar(255) FK | '54f64b5e283b0ce7' |
| event_id_FK | Event_id Foreign Key from the event table | varchar(255) FK | 'dab0e6cb8c932162' |
| winner | Winner of the fighter | varchar(255) | 'Fighter B' |
| performance_bonus | If there was a performance bonus and what type | varchar(255) | NULL |
| weight_class | Weight class of the fight | varchar(255) | 'KO/TKO' |
| method | How the fight ended | varchar(255) | 'KO/TKO' |
| round | What round the fight ended | int | '2' |
| time | What time stamp the fight ended | varchar(255) | '3:32' |
| time_format | The format of the fight, how many rounds of X minutes | varchar(255) | '5 Rnd (5-5-5-5-5)' |
| referee | Referee | varchar(255) | 'Jason Herzog' |
| judge1 | Judge 1 - Is null if the fight didn't go to a decision | varchar(255) | NULL |
| judge2 | Judge 2 - Is null if the fight didn't go to a decision | varchar(255) | NULL |
| judge3 | Judge 3 - Is null if the fight didn't go to a decision | varchar(255) | NULL |
| judge_1_score | Judge 1 Score - Is null if the fight didn't go to a decision | varchar(255) | NULL |
| judge_2_score | Judge 2 Score - Is null if the fight didn't go to a decision | varchar(255) | NULL |
| judge_3_score | Judge 3 Score - Is null if the fight didn't go to a decision | varchar(255) | NULL |
| fighter_a_knockdowns_total | Number of times fighter knockdown their opponent with strikes | int | '0' |
| fighter_b_knockdowns_total | Number of times fighter knockdown their opponent with strikes | int | '1' |
| fighter_a_sig_strikes_landed_total | Number of significant strikes landed | int | '47' |
| fighter_b_sig_strikes_landed_total | Number of significant strikes landed | int | '35' |
| fighter_a_sig_strikes_attempted_total | Number of significant strikes attempted | int | '107' |
| fighter_b_sig_strikes_attempted_total | Number of significant strikes attempted | int | '77' |
| fighter_a_total_strikes_landed_total | Number of total strikes landed | int | '47' |
| fighter_b_total_strikes_landed_total | Number of total strikes landed | int | '36' |
| fighter_a_total_strikes_attempted_total | Number of total strikes attempted | int | '107' |
| fighter_b_total_strikes_attempted_total | Number of total strikes attempted | int | '78' |
| fighter_a_takedowns_total_landed | Number of successful takedowns | int | '0' |
| fighter_b_takedowns_total_landed | Number of successful takedowns | int | '0' |
| fighter_a_takedowns_attempted_total | Number of attempted takedowns | int | '0' |
| fighter_b_takedowns_attempted_total | Number of attempted takedowns | int | '0' |
| fighter_a_submissions_total | Number of submissions attempted | int | '0' |
| fighter_b_submissions_total | Number of submissions attempted | int | '0' |
| fighter_a_reversals_total | A reversal is scored when the bottom fighter actively moves to establish top position, without his opponent getting back to his feet in between. | int | '0' |
| fighter_b_reversals_total | A reversal is scored when the bottom fighter actively moves to establish top position, without his opponent getting back to his feet in between. | int | '0' |
| fighter_a_control_total | Amount of time fighter spends in a top position controlling their opponent | varchar(255) | '0:00' |
| fighter_b_control_total | Amount of time fighter spends in a top position controlling their opponent | varchar(255) | '0:03' |
| fighter_a_sig_head_landed_total | Significant strikes landed at the opponents head | int | '21' |
| fighter_b_sig_head_landed_total | Significant strikes landed at the opponents head | int | '17' |
| fighter_a_sig_head_attempted_total | Significant strikes attempted at the opponents head | int | '68' |
| fighter_b_sig_head_attempted_total | Significant strikes attempted at the opponents head | int | '53' |
| fighter_a_sig_body_landed_total | Significant strikes landed at the opponents body | int | '11' |
| fighter_b_sig_body_landed_total | Significant strikes landed at the opponents body | int | '12' |
| fighter_a_sig_body_attempted_total | Significant strikes attempted at the opponents body | int | '22' |
| fighter_b_sig_body_attempted_total | Significant strikes attempted at the opponents body | int | '16' |
| fighter_a_sig_leg_landed_total | Significant strikes landed at the opponents legs | int | '15' |
| fighter_b_sig_leg_landed_total | Significant strikes landed at the opponents legs | int | '6' |
| fighter_a_sig_leg_attempted_total | Significant strikes attempted at the opponents legs | int | '17' |
| fighter_b_sig_leg_attempted_total | Significant strikes attempted at the opponents legs | int | '8' |
| fighter_a_sig_distance_landed_total | Significant strike landed from distance | int | '44' |
| fighter_b_sig_distance_landed_total | Significant strike landed from distance | int | '28' |
| fighter_a_sig_distance_attempted_total | Significant strike attempted from distance | int | '102' |
| fighter_b_sig_distance_attempted_total | Significant strike attempted from distance | int | '70' |
| fighter_a_sig_clinch_landed_total | Significant strike landed from the clinch | int | '3' |
| fighter_b_sig_clinch_landed_total | Significant strike landed from the clinch | int | '4' |
| fighter_a_sig_clinch_attempted_total | Significant strike attempted from the clinch | int | '5' |
| fighter_b_sig_clinch_attempted_total | Significant strike attempted from the clinch | int | '4' |
| fighter_a_sig_ground_landed_total | Significant strike landed from the ground | int | '0' |
| fighter_b_sig_ground_landed_total | Significant strike landed from ground | int | '3' |
| fighter_a_sig_ground_attempted_total | Significant strike attempted from ground | int | '0' |
| fighter_b_sig_ground_attempted_total | Significant strike attempted from ground | int | '3' |




#### Event Table
The example data comes from the event found at the following URL - http://ufcstats.com/event-details/dab0e6cb8c932162
<br>
<br>

| Column Name | Description | Format | Example |
| --- | --- | --- | --- |
| event_id | Primary key of the event formed from the last part of the URL. 'http://ufcstats.com/event-details/dab0e6cb8c932162' the event_id would be 'dab0e6cb8c932162' | varchar(255) PK | 'dab0e6cb8c932162'|
| name | Event name | varchar(255) | 'UFC 298: Volkanovski vs. Topuria' |
| date | The date the event took place in the format of ... | date 'YYYY-MM-DD' | '2024-02-17' |
| location | Where the event took place. In the format of... | varchar(255) | 'Anaheim, California, USA' |


## Questions and comments
The weight we have is what the fighters fight at but not what we think they weigh when they fight, is there a way to find this information?
The weight class for fight isn't pulling through
Judge Score isn't pulling through
I don't scrape the fight end details if it doesn't go the distance
We don't scrape nicknames
Could expand this data explanation to include things like types of values in a column such as performance bonus.


```python
''' Now we have explained what data we have and what each column represents we now want to know what are 
the typical values, what is the distribution and perform some cleaning.

Part of this would be selecting only data that is useful, however, I am going to perform the data 
cleaning steps on all my data as I wrote the scrapers and may want to change how things are formatted in 
the database after working with them
'''
```




    ' Now we have explained what data we have and what each column represents we now want to know what are \nthe typical values, what is the distribution and perform some cleaning.\n\nPart of this would be selecting only data that is useful, however, I am going to perform the data \ncleaning steps on all my data as I wrote the scrapers and may want to change how things are formatted in \nthe database after working with them\n'




```python
#!pip install mysql-connector-python
#!pip uninstall mqsql-connector

```

## Gather our data
Now that we understand our database structure and the data available to us we need to write a query to get the correct data from out database.


```python
# Import libaries
import mysql.connector
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
import datetime 
from sklearn.preprocessing import LabelEncoder
```


```python
cnx = mysql.connector.connect(user='root', password='Ashtead96',
                              host='localhost',
                              database='ufc_database', auth_plugin='mysql_native_password')

```


```python
query = '''
SELECT 
    f.fight_id, 
    f.fighter_a_id_FK, 
    f.event_id_FK, 
    f.winner, 
    event.name,
    event.date,
    ftr_a.name AS fighter_a_name, 
    ftr_b.name AS fighter_b_name,
    ftr_a.height AS fighter_a_height, 
    ftr_b.height AS fighter_b_height,
    ftr_a.weight AS fighter_a_weight, 
    ftr_b.weight AS fighter_b_weight,
    ftr_a.reach AS fighter_a_reach, 
    ftr_b.reach AS fighter_b_reach,
    ftr_a.stance AS fighter_a_stance, 
    ftr_b.stance AS fighter_b_stance,
    ftr_a.dob AS fighter_a_dob, 
    ftr_b.dob AS fighter_b_dob,
    ftr_a.SLpM AS fighter_a_SLpM, 
    ftr_b.SLpM AS fighter_b_SLpM,
    ftr_a.str_acc AS fighter_a_str_acc, 
    ftr_b.str_acc AS fighter_b_str_acc,
    ftr_a.SApM AS fighter_a_SApM, 
    ftr_b.SApM AS fighter_b_SApM,
    ftr_a.str_def AS fighter_a_str_def, 
    ftr_b.str_def AS fighter_b_str_def,
    ftr_a.TD_avg AS fighter_a_TD_avg, 
    ftr_b.TD_avg AS fighter_b_TD_avg,
    ftr_a.TD_acc AS fighter_a_TD_acc, 
    ftr_b.TD_acc AS fighter_b_TD_acc,
    ftr_a.TD_def AS fighter_a_TD_def, 
    ftr_b.TD_def AS fighter_b_TD_def,
    ftr_a.sub_avg AS fighter_a_sub_avg, 
    ftr_b.sub_avg AS fighter_b_sub_avg
FROM 
    fight f
JOIN 
    fighter ftr_a ON f.fighter_a_id_FK = ftr_a.fighter_id
JOIN 
    fighter ftr_b ON f.fighter_b_id_FK = ftr_b.fighter_id
JOIN event ON f.event_id_FK = event.event_id;
'''

```


```python
cursor = cnx.cursor()
cursor.execute(query)
result = cursor.fetchall()
num_fields = len(cursor.description)
field_names = [i[0] for i in cursor.description]
cursor.close()
```




    True




```python
# First lets have a look at our data
pd.set_option('display.max_columns', None)
dataframe = pd.DataFrame(result, columns=field_names)
dataframe
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>fight_id</th>
      <th>fighter_a_id_FK</th>
      <th>event_id_FK</th>
      <th>winner</th>
      <th>name</th>
      <th>date</th>
      <th>fighter_a_name</th>
      <th>fighter_b_name</th>
      <th>fighter_a_height</th>
      <th>fighter_b_height</th>
      <th>fighter_a_weight</th>
      <th>fighter_b_weight</th>
      <th>fighter_a_reach</th>
      <th>fighter_b_reach</th>
      <th>fighter_a_stance</th>
      <th>fighter_b_stance</th>
      <th>fighter_a_dob</th>
      <th>fighter_b_dob</th>
      <th>fighter_a_SLpM</th>
      <th>fighter_b_SLpM</th>
      <th>fighter_a_str_acc</th>
      <th>fighter_b_str_acc</th>
      <th>fighter_a_SApM</th>
      <th>fighter_b_SApM</th>
      <th>fighter_a_str_def</th>
      <th>fighter_b_str_def</th>
      <th>fighter_a_TD_avg</th>
      <th>fighter_b_TD_avg</th>
      <th>fighter_a_TD_acc</th>
      <th>fighter_b_TD_acc</th>
      <th>fighter_a_TD_def</th>
      <th>fighter_b_TD_def</th>
      <th>fighter_a_sub_avg</th>
      <th>fighter_b_sub_avg</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1a191251620a84ca</td>
      <td>77d7295d1b22c694</td>
      <td>00a905a4a4a2b071</td>
      <td>Fighter B</td>
      <td>UFC Fight Night: Gane vs. Tuivasa</td>
      <td>2022-09-03</td>
      <td>Alessio Di Chirico</td>
      <td>Roman Kopylov</td>
      <td>72.0</td>
      <td>72.0</td>
      <td>185.0</td>
      <td>185.0</td>
      <td>74.0</td>
      <td>75.0</td>
      <td>Orthodox</td>
      <td>Southpaw</td>
      <td>1989-12-12</td>
      <td>1991-05-04</td>
      <td>3.32</td>
      <td>4.72</td>
      <td>39.0</td>
      <td>53.0</td>
      <td>3.44</td>
      <td>4.11</td>
      <td>61.0</td>
      <td>60.0</td>
      <td>1.46</td>
      <td>0.47</td>
      <td>39.0</td>
      <td>50.0</td>
      <td>83.0</td>
      <td>92.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>20f316f96c9e4458</td>
      <td>881bf86d4cba8578</td>
      <td>00a905a4a4a2b071</td>
      <td>Fighter A</td>
      <td>UFC Fight Night: Gane vs. Tuivasa</td>
      <td>2022-09-03</td>
      <td>Nassourdine Imavov</td>
      <td>Joaquin Buckley</td>
      <td>75.0</td>
      <td>70.0</td>
      <td>185.0</td>
      <td>170.0</td>
      <td>75.0</td>
      <td>76.0</td>
      <td>Orthodox</td>
      <td>Southpaw</td>
      <td>1995-03-01</td>
      <td>1994-04-27</td>
      <td>4.53</td>
      <td>3.87</td>
      <td>54.0</td>
      <td>33.0</td>
      <td>3.26</td>
      <td>3.31</td>
      <td>61.0</td>
      <td>57.0</td>
      <td>0.87</td>
      <td>1.51</td>
      <td>31.0</td>
      <td>36.0</td>
      <td>76.0</td>
      <td>65.0</td>
      <td>1.5</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>398db40015b3c81c</td>
      <td>e1d40e8782d80bc2</td>
      <td>00a905a4a4a2b071</td>
      <td>Fighter A</td>
      <td>UFC Fight Night: Gane vs. Tuivasa</td>
      <td>2022-09-03</td>
      <td>William Gomis</td>
      <td>Jarno Errens</td>
      <td>72.0</td>
      <td>71.0</td>
      <td>145.0</td>
      <td>145.0</td>
      <td>73.0</td>
      <td>73.0</td>
      <td>Southpaw</td>
      <td>Orthodox</td>
      <td>1997-06-13</td>
      <td>1994-11-17</td>
      <td>2.62</td>
      <td>1.67</td>
      <td>46.0</td>
      <td>38.0</td>
      <td>1.49</td>
      <td>2.93</td>
      <td>75.0</td>
      <td>52.0</td>
      <td>1.06</td>
      <td>0.00</td>
      <td>60.0</td>
      <td>0.0</td>
      <td>81.0</td>
      <td>33.0</td>
      <td>0.7</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3e2f00991f511607</td>
      <td>c2299ec916bc7c56</td>
      <td>00a905a4a4a2b071</td>
      <td>Fighter A</td>
      <td>UFC Fight Night: Gane vs. Tuivasa</td>
      <td>2022-09-03</td>
      <td>Benoit Saint Denis</td>
      <td>Gabriel Miranda</td>
      <td>71.0</td>
      <td>71.0</td>
      <td>155.0</td>
      <td>145.0</td>
      <td>73.0</td>
      <td>71.0</td>
      <td>Southpaw</td>
      <td>Orthodox</td>
      <td>1995-12-18</td>
      <td>1990-03-25</td>
      <td>5.53</td>
      <td>3.52</td>
      <td>52.0</td>
      <td>51.0</td>
      <td>5.20</td>
      <td>6.88</td>
      <td>44.0</td>
      <td>39.0</td>
      <td>4.55</td>
      <td>4.80</td>
      <td>36.0</td>
      <td>40.0</td>
      <td>66.0</td>
      <td>0.0</td>
      <td>1.4</td>
      <td>2.4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>7bced112f3229b1b</td>
      <td>c21a036b4e012f1c</td>
      <td>00a905a4a4a2b071</td>
      <td>Fighter B</td>
      <td>UFC Fight Night: Gane vs. Tuivasa</td>
      <td>2022-09-03</td>
      <td>John Makdessi</td>
      <td>Nasrat Haqparast</td>
      <td>68.0</td>
      <td>70.0</td>
      <td>155.0</td>
      <td>155.0</td>
      <td>68.0</td>
      <td>72.0</td>
      <td>Orthodox</td>
      <td>Southpaw</td>
      <td>1985-05-03</td>
      <td>1995-08-22</td>
      <td>5.52</td>
      <td>5.78</td>
      <td>49.0</td>
      <td>44.0</td>
      <td>4.15</td>
      <td>5.25</td>
      <td>68.0</td>
      <td>64.0</td>
      <td>0.00</td>
      <td>0.31</td>
      <td>0.0</td>
      <td>20.0</td>
      <td>86.0</td>
      <td>78.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>7514</th>
      <td>79817ab0129c5598</td>
      <td>634bb0de2eb043b4</td>
      <td>ff9578cdbfabd323</td>
      <td>Fighter A</td>
      <td>UFC 214: Cormier vs. Jones 2</td>
      <td>2017-07-29</td>
      <td>Cristiane Justino</td>
      <td>Tonya Evinger</td>
      <td>68.0</td>
      <td>67.0</td>
      <td>145.0</td>
      <td>135.0</td>
      <td>68.0</td>
      <td>70.0</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1985-07-09</td>
      <td>1981-06-04</td>
      <td>7.28</td>
      <td>1.16</td>
      <td>52.0</td>
      <td>42.0</td>
      <td>2.25</td>
      <td>4.02</td>
      <td>64.0</td>
      <td>38.0</td>
      <td>0.66</td>
      <td>0.93</td>
      <td>55.0</td>
      <td>30.0</td>
      <td>94.0</td>
      <td>33.0</td>
      <td>0.4</td>
      <td>0.9</td>
    </tr>
    <tr>
      <th>7515</th>
      <td>ac9e19a4075f6557</td>
      <td>cb696ebfb6598724</td>
      <td>ff9578cdbfabd323</td>
      <td>Fighter A</td>
      <td>UFC 214: Cormier vs. Jones 2</td>
      <td>2017-07-29</td>
      <td>Aljamain Sterling</td>
      <td>Renan Barao</td>
      <td>67.0</td>
      <td>66.0</td>
      <td>145.0</td>
      <td>145.0</td>
      <td>71.0</td>
      <td>70.0</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1989-07-31</td>
      <td>1987-01-31</td>
      <td>4.73</td>
      <td>3.78</td>
      <td>52.0</td>
      <td>36.0</td>
      <td>2.41</td>
      <td>3.90</td>
      <td>58.0</td>
      <td>57.0</td>
      <td>1.97</td>
      <td>1.49</td>
      <td>24.0</td>
      <td>41.0</td>
      <td>45.0</td>
      <td>91.0</td>
      <td>0.8</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>7516</th>
      <td>b8c967c742d3c107</td>
      <td>f2925e6db404bf1d</td>
      <td>ff9578cdbfabd323</td>
      <td>Fighter A</td>
      <td>UFC 214: Cormier vs. Jones 2</td>
      <td>2017-07-29</td>
      <td>Robbie Lawler</td>
      <td>Donald Cerrone</td>
      <td>71.0</td>
      <td>73.0</td>
      <td>170.0</td>
      <td>155.0</td>
      <td>74.0</td>
      <td>73.0</td>
      <td>Southpaw</td>
      <td>Orthodox</td>
      <td>1982-03-20</td>
      <td>1983-03-29</td>
      <td>3.85</td>
      <td>4.41</td>
      <td>47.0</td>
      <td>46.0</td>
      <td>4.67</td>
      <td>4.48</td>
      <td>59.0</td>
      <td>53.0</td>
      <td>0.65</td>
      <td>1.16</td>
      <td>64.0</td>
      <td>33.0</td>
      <td>65.0</td>
      <td>73.0</td>
      <td>0.0</td>
      <td>1.2</td>
    </tr>
    <tr>
      <th>7517</th>
      <td>dffad417175400ba</td>
      <td>3974fa35c917af1d</td>
      <td>ff9578cdbfabd323</td>
      <td>Fighter A</td>
      <td>UFC 214: Cormier vs. Jones 2</td>
      <td>2017-07-29</td>
      <td>Ricardo Lamas</td>
      <td>Jason Knight</td>
      <td>68.0</td>
      <td>70.0</td>
      <td>145.0</td>
      <td>145.0</td>
      <td>71.0</td>
      <td>71.0</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1982-05-21</td>
      <td>1992-07-14</td>
      <td>3.13</td>
      <td>2.95</td>
      <td>47.0</td>
      <td>33.0</td>
      <td>2.87</td>
      <td>3.02</td>
      <td>57.0</td>
      <td>51.0</td>
      <td>1.84</td>
      <td>1.31</td>
      <td>33.0</td>
      <td>31.0</td>
      <td>46.0</td>
      <td>47.0</td>
      <td>0.9</td>
      <td>1.7</td>
    </tr>
    <tr>
      <th>7518</th>
      <td>e80a8338d86e730b</td>
      <td>05b43e0ead3df345</td>
      <td>ff9578cdbfabd323</td>
      <td>Fighter B</td>
      <td>UFC 214: Cormier vs. Jones 2</td>
      <td>2017-07-29</td>
      <td>Kailin Curran</td>
      <td>Aleksandra Albu</td>
      <td>64.0</td>
      <td>62.0</td>
      <td>115.0</td>
      <td>115.0</td>
      <td>65.0</td>
      <td>63.0</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1991-04-11</td>
      <td>1990-07-14</td>
      <td>3.89</td>
      <td>4.83</td>
      <td>43.0</td>
      <td>47.0</td>
      <td>4.75</td>
      <td>5.56</td>
      <td>53.0</td>
      <td>38.0</td>
      <td>2.00</td>
      <td>2.27</td>
      <td>57.0</td>
      <td>50.0</td>
      <td>61.0</td>
      <td>77.0</td>
      <td>0.6</td>
      <td>0.4</td>
    </tr>
  </tbody>
</table>
<p>7519 rows × 34 columns</p>
</div>




```python
# Lets look at some basic information about our data
dataframe.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 7519 entries, 0 to 7518
    Data columns (total 34 columns):
     #   Column             Non-Null Count  Dtype  
    ---  ------             --------------  -----  
     0   fight_id           7519 non-null   object 
     1   fighter_a_id_FK    7519 non-null   object 
     2   event_id_FK        7519 non-null   object 
     3   winner             7519 non-null   object 
     4   name               7519 non-null   object 
     5   date               7519 non-null   object 
     6   fighter_a_name     7519 non-null   object 
     7   fighter_b_name     7519 non-null   object 
     8   fighter_a_height   7514 non-null   float64
     9   fighter_b_height   7497 non-null   float64
     10  fighter_a_weight   7516 non-null   float64
     11  fighter_b_weight   7500 non-null   float64
     12  fighter_a_reach    7098 non-null   float64
     13  fighter_b_reach    6615 non-null   float64
     14  fighter_a_stance   7493 non-null   object 
     15  fighter_b_stance   7448 non-null   object 
     16  fighter_a_dob      7442 non-null   object 
     17  fighter_b_dob      7327 non-null   object 
     18  fighter_a_SLpM     7519 non-null   float64
     19  fighter_b_SLpM     7519 non-null   float64
     20  fighter_a_str_acc  7519 non-null   float64
     21  fighter_b_str_acc  7519 non-null   float64
     22  fighter_a_SApM     7519 non-null   float64
     23  fighter_b_SApM     7519 non-null   float64
     24  fighter_a_str_def  7519 non-null   float64
     25  fighter_b_str_def  7519 non-null   float64
     26  fighter_a_TD_avg   7519 non-null   float64
     27  fighter_b_TD_avg   7519 non-null   float64
     28  fighter_a_TD_acc   7519 non-null   float64
     29  fighter_b_TD_acc   7519 non-null   float64
     30  fighter_a_TD_def   7519 non-null   float64
     31  fighter_b_TD_def   7519 non-null   float64
     32  fighter_a_sub_avg  7519 non-null   float64
     33  fighter_b_sub_avg  7519 non-null   float64
    dtypes: float64(22), object(12)
    memory usage: 2.0+ MB
    


```python
# Lets describe our dataset - We are trying to understand which of our columns are categorical and 
# which are continous as this will change how we deal with them in later steps
empty_values = dataframe.isna().sum()
empty_values = empty_values[empty_values > 0]
empty_values
```




    fighter_a_height      5
    fighter_b_height     22
    fighter_a_weight      3
    fighter_b_weight     19
    fighter_a_reach     421
    fighter_b_reach     904
    fighter_a_stance     26
    fighter_b_stance     71
    fighter_a_dob        77
    fighter_b_dob       192
    dtype: int64



When we look at the missing values we can see some values are missing much more than other. As a general rule we don't want to lose information if we can help it particularly if we think it will be useful for our model.

The missing values in this dataset I would suspect is caused by a combination of two things:

1. Earlier fights in the UFC don't have as strong stats records
2. Fighters that had a shorter career (usually due to them being released by the UFC) often don't have full stats, this is particularly common when the fight is also an earlier fight.

Let's investigate!!




```python
# Investigating the missing 904 values from 'fighter_b_reach'
#dataframe[dataframe['fighter_b_reach'].isna()]
missing_value_data = dataframe[dataframe.isna().any(axis=1)]
missing_value_data
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>fight_id</th>
      <th>fighter_a_id_FK</th>
      <th>event_id_FK</th>
      <th>winner</th>
      <th>name</th>
      <th>date</th>
      <th>fighter_a_name</th>
      <th>fighter_b_name</th>
      <th>fighter_a_height</th>
      <th>fighter_b_height</th>
      <th>fighter_a_weight</th>
      <th>fighter_b_weight</th>
      <th>fighter_a_reach</th>
      <th>fighter_b_reach</th>
      <th>fighter_a_stance</th>
      <th>fighter_b_stance</th>
      <th>fighter_a_dob</th>
      <th>fighter_b_dob</th>
      <th>fighter_a_SLpM</th>
      <th>fighter_b_SLpM</th>
      <th>fighter_a_str_acc</th>
      <th>fighter_b_str_acc</th>
      <th>fighter_a_SApM</th>
      <th>fighter_b_SApM</th>
      <th>fighter_a_str_def</th>
      <th>fighter_b_str_def</th>
      <th>fighter_a_TD_avg</th>
      <th>fighter_b_TD_avg</th>
      <th>fighter_a_TD_acc</th>
      <th>fighter_b_TD_acc</th>
      <th>fighter_a_TD_def</th>
      <th>fighter_b_TD_def</th>
      <th>fighter_a_sub_avg</th>
      <th>fighter_b_sub_avg</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>60</th>
      <td>3c2ac653409cf5f2</td>
      <td>0e041d43a47d2e4b</td>
      <td>02fc8f50f56eb307</td>
      <td>Fighter A</td>
      <td>UFC 36: Worlds Collide</td>
      <td>2002-03-22</td>
      <td>Matt Lindland</td>
      <td>Pat Miletich</td>
      <td>72.0</td>
      <td>70.0</td>
      <td>185.0</td>
      <td>170.0</td>
      <td>74.0</td>
      <td>NaN</td>
      <td>Southpaw</td>
      <td>Orthodox</td>
      <td>1970-05-17</td>
      <td>1968-03-09</td>
      <td>2.62</td>
      <td>1.79</td>
      <td>50.0</td>
      <td>45.0</td>
      <td>1.56</td>
      <td>1.66</td>
      <td>52.0</td>
      <td>63.0</td>
      <td>3.01</td>
      <td>1.96</td>
      <td>48.0</td>
      <td>100.0</td>
      <td>60.0</td>
      <td>54.0</td>
      <td>1.3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>61</th>
      <td>7468f870b08aaccc</td>
      <td>1ff9589f9065a9ed</td>
      <td>02fc8f50f56eb307</td>
      <td>Fighter A</td>
      <td>UFC 36: Worlds Collide</td>
      <td>2002-03-22</td>
      <td>Frank Mir</td>
      <td>Pete Williams</td>
      <td>75.0</td>
      <td>75.0</td>
      <td>264.0</td>
      <td>235.0</td>
      <td>79.0</td>
      <td>NaN</td>
      <td>Southpaw</td>
      <td>Orthodox</td>
      <td>1979-05-24</td>
      <td>1975-07-10</td>
      <td>2.18</td>
      <td>0.61</td>
      <td>47.0</td>
      <td>62.0</td>
      <td>3.84</td>
      <td>2.77</td>
      <td>38.0</td>
      <td>40.0</td>
      <td>2.02</td>
      <td>1.84</td>
      <td>40.0</td>
      <td>40.0</td>
      <td>54.0</td>
      <td>16.0</td>
      <td>2.0</td>
      <td>0.9</td>
    </tr>
    <tr>
      <th>62</th>
      <td>87f39e0d82349c3b</td>
      <td>029880cdbf5ca089</td>
      <td>02fc8f50f56eb307</td>
      <td>Fighter A</td>
      <td>UFC 36: Worlds Collide</td>
      <td>2002-03-22</td>
      <td>Sean Sherk</td>
      <td>Jutaro Nakao</td>
      <td>66.0</td>
      <td>69.0</td>
      <td>155.0</td>
      <td>170.0</td>
      <td>67.0</td>
      <td>NaN</td>
      <td>Orthodox</td>
      <td>Southpaw</td>
      <td>1973-08-05</td>
      <td>1970-11-30</td>
      <td>2.17</td>
      <td>0.53</td>
      <td>35.0</td>
      <td>38.0</td>
      <td>2.71</td>
      <td>1.06</td>
      <td>55.0</td>
      <td>44.0</td>
      <td>4.09</td>
      <td>0.38</td>
      <td>46.0</td>
      <td>50.0</td>
      <td>56.0</td>
      <td>56.0</td>
      <td>0.4</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>63</th>
      <td>e7833d5728f3ebef</td>
      <td>621a6c59f88a44fe</td>
      <td>02fc8f50f56eb307</td>
      <td>Fighter A</td>
      <td>UFC 36: Worlds Collide</td>
      <td>2002-03-22</td>
      <td>Matt Hughes</td>
      <td>Hayato Sakurai</td>
      <td>69.0</td>
      <td>67.0</td>
      <td>170.0</td>
      <td>168.0</td>
      <td>73.0</td>
      <td>NaN</td>
      <td>Switch</td>
      <td>Orthodox</td>
      <td>1973-10-13</td>
      <td>1975-08-24</td>
      <td>2.14</td>
      <td>2.45</td>
      <td>53.0</td>
      <td>49.0</td>
      <td>1.36</td>
      <td>1.94</td>
      <td>53.0</td>
      <td>59.0</td>
      <td>2.95</td>
      <td>1.87</td>
      <td>50.0</td>
      <td>69.0</td>
      <td>35.0</td>
      <td>56.0</td>
      <td>1.2</td>
      <td>0.3</td>
    </tr>
    <tr>
      <th>64</th>
      <td>ef83377d584ec055</td>
      <td>86dfed7cc24a9fa7</td>
      <td>02fc8f50f56eb307</td>
      <td>Fighter A</td>
      <td>UFC 36: Worlds Collide</td>
      <td>2002-03-22</td>
      <td>Matt Serra</td>
      <td>Kelly Dullanty</td>
      <td>66.0</td>
      <td>68.0</td>
      <td>170.0</td>
      <td>155.0</td>
      <td>68.0</td>
      <td>NaN</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1974-06-02</td>
      <td>1977-11-04</td>
      <td>1.98</td>
      <td>0.00</td>
      <td>39.0</td>
      <td>0.0</td>
      <td>2.64</td>
      <td>0.67</td>
      <td>46.0</td>
      <td>75.0</td>
      <td>2.05</td>
      <td>0.00</td>
      <td>20.0</td>
      <td>0.0</td>
      <td>38.0</td>
      <td>66.0</td>
      <td>0.9</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>7422</th>
      <td>ddb950831580a5fe</td>
      <td>84a067c46306a737</td>
      <td>fbbde91f7bc2d3c5</td>
      <td>Fighter A</td>
      <td>The Ultimate Fighter: Team Couture vs. Team Li...</td>
      <td>2005-04-09</td>
      <td>Sam Hoger</td>
      <td>Bobby Southworth</td>
      <td>74.0</td>
      <td>74.0</td>
      <td>205.0</td>
      <td>205.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1980-06-28</td>
      <td>1969-12-16</td>
      <td>1.07</td>
      <td>0.99</td>
      <td>25.0</td>
      <td>39.0</td>
      <td>2.25</td>
      <td>1.38</td>
      <td>57.0</td>
      <td>65.0</td>
      <td>1.12</td>
      <td>3.98</td>
      <td>38.0</td>
      <td>78.0</td>
      <td>29.0</td>
      <td>64.0</td>
      <td>1.3</td>
      <td>1.3</td>
    </tr>
    <tr>
      <th>7423</th>
      <td>f434775e97920d95</td>
      <td>52cae54377b433b7</td>
      <td>fbbde91f7bc2d3c5</td>
      <td>Fighter A</td>
      <td>The Ultimate Fighter: Team Couture vs. Team Li...</td>
      <td>2005-04-09</td>
      <td>Nate Quarry</td>
      <td>Lodune Sincaid</td>
      <td>72.0</td>
      <td>69.0</td>
      <td>185.0</td>
      <td>185.0</td>
      <td>72.0</td>
      <td>NaN</td>
      <td>Open Stance</td>
      <td>Orthodox</td>
      <td>1972-03-18</td>
      <td>1973-05-07</td>
      <td>4.96</td>
      <td>2.72</td>
      <td>44.0</td>
      <td>42.0</td>
      <td>2.80</td>
      <td>6.17</td>
      <td>66.0</td>
      <td>57.0</td>
      <td>0.25</td>
      <td>0.00</td>
      <td>33.0</td>
      <td>0.0</td>
      <td>60.0</td>
      <td>75.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>7434</th>
      <td>f8bb0aed83bd8638</td>
      <td>27b164b0d1833df0</td>
      <td>fc1868f56d3036eb</td>
      <td>Fighter B</td>
      <td>UFC Fight Night: Stephens vs. Choi</td>
      <td>2018-01-14</td>
      <td>Kalindra Faria</td>
      <td>Jessica Eye</td>
      <td>67.0</td>
      <td>66.0</td>
      <td>125.0</td>
      <td>125.0</td>
      <td>NaN</td>
      <td>66.0</td>
      <td>Switch</td>
      <td>Orthodox</td>
      <td>1986-07-23</td>
      <td>1986-07-27</td>
      <td>2.23</td>
      <td>3.86</td>
      <td>56.0</td>
      <td>37.0</td>
      <td>1.44</td>
      <td>4.19</td>
      <td>45.0</td>
      <td>55.0</td>
      <td>1.31</td>
      <td>0.58</td>
      <td>66.0</td>
      <td>40.0</td>
      <td>20.0</td>
      <td>57.0</td>
      <td>0.7</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>7473</th>
      <td>2573a9a722f238f4</td>
      <td>ca2efc9d7d523c43</td>
      <td>fd7acf42bd6e7e95</td>
      <td>Fighter A</td>
      <td>UFC Fight Night: Bader vs Saint Preux</td>
      <td>2014-08-16</td>
      <td>Shawn Jordan</td>
      <td>Jack May</td>
      <td>72.0</td>
      <td>80.0</td>
      <td>260.0</td>
      <td>255.0</td>
      <td>75.0</td>
      <td>NaN</td>
      <td>Southpaw</td>
      <td>Switch</td>
      <td>1984-10-21</td>
      <td>1981-04-14</td>
      <td>2.87</td>
      <td>1.52</td>
      <td>50.0</td>
      <td>38.0</td>
      <td>3.30</td>
      <td>3.29</td>
      <td>47.0</td>
      <td>33.0</td>
      <td>1.97</td>
      <td>0.91</td>
      <td>38.0</td>
      <td>33.0</td>
      <td>76.0</td>
      <td>20.0</td>
      <td>0.1</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>7478</th>
      <td>99dc6aa635a9cf98</td>
      <td>9eb069062a0cfc13</td>
      <td>fd7acf42bd6e7e95</td>
      <td>Fighter B</td>
      <td>UFC Fight Night: Bader vs Saint Preux</td>
      <td>2014-08-16</td>
      <td>Nolan Ticman</td>
      <td>Frankie Saenz</td>
      <td>66.0</td>
      <td>66.0</td>
      <td>125.0</td>
      <td>135.0</td>
      <td>NaN</td>
      <td>66.0</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1988-05-17</td>
      <td>1980-08-12</td>
      <td>2.57</td>
      <td>3.94</td>
      <td>47.0</td>
      <td>47.0</td>
      <td>2.53</td>
      <td>3.50</td>
      <td>67.0</td>
      <td>52.0</td>
      <td>0.00</td>
      <td>1.74</td>
      <td>0.0</td>
      <td>31.0</td>
      <td>60.0</td>
      <td>61.0</td>
      <td>0.0</td>
      <td>0.1</td>
    </tr>
  </tbody>
</table>
<p>1068 rows × 34 columns</p>
</div>




```python
# Lets have a look how many missing values are from earlier UFC events - We will make a visual of this
# so it is easier to see!

# first convert the column to a datetime so we can filter
missing_value_data['date'] = pd.to_datetime(missing_value_data['date'], format='%Y-%m-%d')

# next lets declare a year list so we can see what percentage at each year is missing
years = list(range(2000, 2021))
missing_values = [len(missing_value_data[missing_value_data['date'] < str(year)+'-01-01']) 
                  for year in years]

# calculate the percentages so we can label out boxes
total_missing_values = len(missing_value_data['date'])
percentages = [(count / total_missing_values) * 100 for count in missing_values]

# create the bar chart
plt.figure(figsize=(10, 6))
ax = sns.barplot(years, missing_values, color='turquoise')
plt.xlabel('Year')
plt.ylabel('Count of Missing Values')
plt.title('Count of Missing Values Before Each Year')
plt.xticks(rotation=45)

# Add percentage labels
for i, p in enumerate(ax.patches):
    height = p.get_height()
    ax.text(p.get_x() + p.get_width() / 2.,
            height + 0.1,
            f'{percentages[i]:.2f}%',
            ha='center', va='bottom', rotation=90)

plt.tight_layout()

# show the plot
plt.show()
```

    C:\Users\lanna\Anaconda3\lib\site-packages\ipykernel_launcher.py:5: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      """
    C:\Users\lanna\Anaconda3\lib\site-packages\seaborn\_decorators.py:43: FutureWarning: Pass the following variables as keyword args: x, y. From version 0.12, the only valid positional argument will be `data`, and passing other arguments without an explicit keyword will result in an error or misinterpretation.
      FutureWarning
    


    
![png](output_14_1.png)
    


Looking at the the chart above we can see that 77.43% of missing values occur before 2014 as we suspected. We could drop these values but we would lose roughly 700 rows which is about 10% of our data! I don't want to lose that much information so I will impute these missing values.

Before we impute values we need to do two things:
1. Check that our data is in the correct format
2. Understand the distribution of our data

These are important steps as how we impute missing values will depend on what sort of data we have and what distribution that data falls under. For example, if we have continuous data we can impute the mean or median but for categorical we might want to use the mode. For the distribution, if our distribution is normal we can use the mean, but if the distribution is skewed this will artificially increase or decrease the mean as it is sensitive to outliers, in this situation the median value may be more appropriate.


```python
# Lets check our data types are correct
dataframe.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 7519 entries, 0 to 7518
    Data columns (total 34 columns):
     #   Column             Non-Null Count  Dtype  
    ---  ------             --------------  -----  
     0   fight_id           7519 non-null   object 
     1   fighter_a_id_FK    7519 non-null   object 
     2   event_id_FK        7519 non-null   object 
     3   winner             7519 non-null   object 
     4   name               7519 non-null   object 
     5   date               7519 non-null   object 
     6   fighter_a_name     7519 non-null   object 
     7   fighter_b_name     7519 non-null   object 
     8   fighter_a_height   7514 non-null   float64
     9   fighter_b_height   7497 non-null   float64
     10  fighter_a_weight   7516 non-null   float64
     11  fighter_b_weight   7500 non-null   float64
     12  fighter_a_reach    7098 non-null   float64
     13  fighter_b_reach    6615 non-null   float64
     14  fighter_a_stance   7493 non-null   object 
     15  fighter_b_stance   7448 non-null   object 
     16  fighter_a_dob      7442 non-null   object 
     17  fighter_b_dob      7327 non-null   object 
     18  fighter_a_SLpM     7519 non-null   float64
     19  fighter_b_SLpM     7519 non-null   float64
     20  fighter_a_str_acc  7519 non-null   float64
     21  fighter_b_str_acc  7519 non-null   float64
     22  fighter_a_SApM     7519 non-null   float64
     23  fighter_b_SApM     7519 non-null   float64
     24  fighter_a_str_def  7519 non-null   float64
     25  fighter_b_str_def  7519 non-null   float64
     26  fighter_a_TD_avg   7519 non-null   float64
     27  fighter_b_TD_avg   7519 non-null   float64
     28  fighter_a_TD_acc   7519 non-null   float64
     29  fighter_b_TD_acc   7519 non-null   float64
     30  fighter_a_TD_def   7519 non-null   float64
     31  fighter_b_TD_def   7519 non-null   float64
     32  fighter_a_sub_avg  7519 non-null   float64
     33  fighter_b_sub_avg  7519 non-null   float64
    dtypes: float64(22), object(12)
    memory usage: 2.0+ MB
    

We can see from the above most of our data is in the format of float64, which is what we expect given what the columns represent. We have some object values though which normally represent text values. Looking at our table information we can work out some of these columns are categorical variables stored as text such as stance and winner. To deal with this we should encode these categorical variable as numeric values so we can work with them easily. 

In addition, some of the other values aren't required such as names...

Finally, the DOB column requires some feature engineering to convert it into age. We will note this down to complete in the feature engineering section later!


```python
# First we are going to encode the stance columns

# Let check the values to make sure we don't have any weird values we weren't expecting
print(dataframe['fighter_a_stance'].value_counts())
print(dataframe['fighter_b_stance'].value_counts())
```

    Orthodox       5623
    Southpaw       1506
    Switch          347
    Open Stance      15
    Sideways          2
    Name: fighter_a_stance, dtype: int64
    Orthodox       5588
    Southpaw       1446
    Switch          401
    Open Stance       9
    Sideways          4
    Name: fighter_b_stance, dtype: int64
    


```python
# So we have some values I wasn't expecting in 'Open Stance' & 'Sideways' lets have a look at these values
# before we decide what to do with them
dataframe.loc[dataframe['fighter_a_stance'] == 'Open Stance']
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>fight_id</th>
      <th>fighter_a_id_FK</th>
      <th>event_id_FK</th>
      <th>winner</th>
      <th>name</th>
      <th>date</th>
      <th>fighter_a_name</th>
      <th>fighter_b_name</th>
      <th>fighter_a_height</th>
      <th>fighter_b_height</th>
      <th>fighter_a_weight</th>
      <th>fighter_b_weight</th>
      <th>fighter_a_reach</th>
      <th>fighter_b_reach</th>
      <th>fighter_a_stance</th>
      <th>fighter_b_stance</th>
      <th>fighter_a_dob</th>
      <th>fighter_b_dob</th>
      <th>fighter_a_SLpM</th>
      <th>fighter_b_SLpM</th>
      <th>fighter_a_str_acc</th>
      <th>fighter_b_str_acc</th>
      <th>fighter_a_SApM</th>
      <th>fighter_b_SApM</th>
      <th>fighter_a_str_def</th>
      <th>fighter_b_str_def</th>
      <th>fighter_a_TD_avg</th>
      <th>fighter_b_TD_avg</th>
      <th>fighter_a_TD_acc</th>
      <th>fighter_b_TD_acc</th>
      <th>fighter_a_TD_def</th>
      <th>fighter_b_TD_def</th>
      <th>fighter_a_sub_avg</th>
      <th>fighter_b_sub_avg</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>392</th>
      <td>5e3f46e6f46d5a90</td>
      <td>59583ff832fe9d68</td>
      <td>0ff11cc094e887bc</td>
      <td>Fighter A</td>
      <td>UFC 122: Marquardt vs Okami</td>
      <td>2010-11-13</td>
      <td>Krzysztof Soszynski</td>
      <td>Goran Reljic</td>
      <td>73.0</td>
      <td>75.0</td>
      <td>205.0</td>
      <td>205.0</td>
      <td>77.0</td>
      <td>81.0</td>
      <td>Open Stance</td>
      <td>Southpaw</td>
      <td>1977-08-02</td>
      <td>1984-03-20</td>
      <td>3.37</td>
      <td>1.69</td>
      <td>39.0</td>
      <td>37.0</td>
      <td>3.13</td>
      <td>2.69</td>
      <td>58.0</td>
      <td>59.0</td>
      <td>0.52</td>
      <td>1.69</td>
      <td>25.0</td>
      <td>50.0</td>
      <td>71.0</td>
      <td>33.0</td>
      <td>1.0</td>
      <td>1.1</td>
    </tr>
    <tr>
      <th>490</th>
      <td>536c26ef4da9ace7</td>
      <td>59583ff832fe9d68</td>
      <td>140745cbbcb023ac</td>
      <td>Fighter B</td>
      <td>UFC 116: Lesnar vs Carwin</td>
      <td>2010-07-03</td>
      <td>Krzysztof Soszynski</td>
      <td>Stephan Bonnar</td>
      <td>73.0</td>
      <td>76.0</td>
      <td>205.0</td>
      <td>205.0</td>
      <td>77.0</td>
      <td>79.0</td>
      <td>Open Stance</td>
      <td>Orthodox</td>
      <td>1977-08-02</td>
      <td>1977-04-04</td>
      <td>3.37</td>
      <td>2.76</td>
      <td>39.0</td>
      <td>38.0</td>
      <td>3.13</td>
      <td>3.01</td>
      <td>58.0</td>
      <td>52.0</td>
      <td>0.52</td>
      <td>1.32</td>
      <td>25.0</td>
      <td>40.0</td>
      <td>71.0</td>
      <td>60.0</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>605</th>
      <td>1f64e532f942ef57</td>
      <td>59583ff832fe9d68</td>
      <td>1652f3213655b935</td>
      <td>Fighter A</td>
      <td>UFC 97: Redemption</td>
      <td>2009-04-18</td>
      <td>Krzysztof Soszynski</td>
      <td>Brian Stann</td>
      <td>73.0</td>
      <td>73.0</td>
      <td>205.0</td>
      <td>205.0</td>
      <td>77.0</td>
      <td>74.0</td>
      <td>Open Stance</td>
      <td>Orthodox</td>
      <td>1977-08-02</td>
      <td>1980-09-24</td>
      <td>3.37</td>
      <td>3.28</td>
      <td>39.0</td>
      <td>42.0</td>
      <td>3.13</td>
      <td>2.65</td>
      <td>58.0</td>
      <td>59.0</td>
      <td>0.52</td>
      <td>0.12</td>
      <td>25.0</td>
      <td>12.0</td>
      <td>71.0</td>
      <td>60.0</td>
      <td>1.0</td>
      <td>0.3</td>
    </tr>
    <tr>
      <th>608</th>
      <td>793432d042384a02</td>
      <td>52cae54377b433b7</td>
      <td>1652f3213655b935</td>
      <td>Fighter A</td>
      <td>UFC 97: Redemption</td>
      <td>2009-04-18</td>
      <td>Nate Quarry</td>
      <td>Jason MacDonald</td>
      <td>72.0</td>
      <td>75.0</td>
      <td>185.0</td>
      <td>185.0</td>
      <td>72.0</td>
      <td>80.0</td>
      <td>Open Stance</td>
      <td>Orthodox</td>
      <td>1972-03-18</td>
      <td>1975-06-03</td>
      <td>4.96</td>
      <td>1.55</td>
      <td>44.0</td>
      <td>52.0</td>
      <td>2.80</td>
      <td>2.70</td>
      <td>66.0</td>
      <td>46.0</td>
      <td>0.25</td>
      <td>1.43</td>
      <td>33.0</td>
      <td>16.0</td>
      <td>60.0</td>
      <td>35.0</td>
      <td>0.0</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>1990</th>
      <td>516868e246064e2b</td>
      <td>52cae54377b433b7</td>
      <td>3ed134d85dfbd7b4</td>
      <td>Fighter B</td>
      <td>UFC Fight Night: Florian vs Gomi</td>
      <td>2010-03-31</td>
      <td>Nate Quarry</td>
      <td>Jorge Rivera</td>
      <td>72.0</td>
      <td>73.0</td>
      <td>185.0</td>
      <td>185.0</td>
      <td>72.0</td>
      <td>73.0</td>
      <td>Open Stance</td>
      <td>Orthodox</td>
      <td>1972-03-18</td>
      <td>1972-02-28</td>
      <td>4.96</td>
      <td>3.16</td>
      <td>44.0</td>
      <td>48.0</td>
      <td>2.80</td>
      <td>2.64</td>
      <td>66.0</td>
      <td>54.0</td>
      <td>0.25</td>
      <td>0.84</td>
      <td>33.0</td>
      <td>50.0</td>
      <td>60.0</td>
      <td>63.0</td>
      <td>0.0</td>
      <td>0.1</td>
    </tr>
    <tr>
      <th>2003</th>
      <td>bf1525f9761a116e</td>
      <td>52cae54377b433b7</td>
      <td>3f24c96753dbd9f9</td>
      <td>Fighter A</td>
      <td>UFC Fight Night 1</td>
      <td>2005-08-06</td>
      <td>Nate Quarry</td>
      <td>Pete Sell</td>
      <td>72.0</td>
      <td>71.0</td>
      <td>185.0</td>
      <td>170.0</td>
      <td>72.0</td>
      <td>75.0</td>
      <td>Open Stance</td>
      <td>Orthodox</td>
      <td>1972-03-18</td>
      <td>1982-08-05</td>
      <td>4.96</td>
      <td>2.60</td>
      <td>44.0</td>
      <td>42.0</td>
      <td>2.80</td>
      <td>3.65</td>
      <td>66.0</td>
      <td>51.0</td>
      <td>0.25</td>
      <td>0.91</td>
      <td>33.0</td>
      <td>21.0</td>
      <td>60.0</td>
      <td>47.0</td>
      <td>0.0</td>
      <td>0.9</td>
    </tr>
    <tr>
      <th>2383</th>
      <td>0ea087a71863184d</td>
      <td>59583ff832fe9d68</td>
      <td>49efbdc6c9f650c4</td>
      <td>Fighter A</td>
      <td>UFC 110: Nogueira vs Velasquez</td>
      <td>2010-02-20</td>
      <td>Krzysztof Soszynski</td>
      <td>Stephan Bonnar</td>
      <td>73.0</td>
      <td>76.0</td>
      <td>205.0</td>
      <td>205.0</td>
      <td>77.0</td>
      <td>79.0</td>
      <td>Open Stance</td>
      <td>Orthodox</td>
      <td>1977-08-02</td>
      <td>1977-04-04</td>
      <td>3.37</td>
      <td>2.76</td>
      <td>39.0</td>
      <td>38.0</td>
      <td>3.13</td>
      <td>3.01</td>
      <td>58.0</td>
      <td>52.0</td>
      <td>0.52</td>
      <td>1.32</td>
      <td>25.0</td>
      <td>40.0</td>
      <td>71.0</td>
      <td>60.0</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>3342</th>
      <td>e03ec6b24fca2386</td>
      <td>59583ff832fe9d68</td>
      <td>6d7886b094b471ac</td>
      <td>Fighter B</td>
      <td>UFC 140: Jones vs Machida</td>
      <td>2011-12-10</td>
      <td>Krzysztof Soszynski</td>
      <td>Igor Pokrajac</td>
      <td>73.0</td>
      <td>72.0</td>
      <td>205.0</td>
      <td>205.0</td>
      <td>77.0</td>
      <td>74.0</td>
      <td>Open Stance</td>
      <td>Orthodox</td>
      <td>1977-08-02</td>
      <td>1979-01-02</td>
      <td>3.37</td>
      <td>2.25</td>
      <td>39.0</td>
      <td>45.0</td>
      <td>3.13</td>
      <td>4.26</td>
      <td>58.0</td>
      <td>40.0</td>
      <td>0.52</td>
      <td>0.87</td>
      <td>25.0</td>
      <td>29.0</td>
      <td>71.0</td>
      <td>51.0</td>
      <td>1.0</td>
      <td>0.2</td>
    </tr>
    <tr>
      <th>5006</th>
      <td>b589aba75770bc6b</td>
      <td>52cae54377b433b7</td>
      <td>a8ea84cbe1655f0a</td>
      <td>Fighter A</td>
      <td>UFC Fight Night: Diaz vs Guillard</td>
      <td>2009-09-16</td>
      <td>Nate Quarry</td>
      <td>Tim Credeur</td>
      <td>72.0</td>
      <td>75.0</td>
      <td>185.0</td>
      <td>185.0</td>
      <td>72.0</td>
      <td>75.0</td>
      <td>Open Stance</td>
      <td>Orthodox</td>
      <td>1972-03-18</td>
      <td>1977-07-09</td>
      <td>4.96</td>
      <td>3.59</td>
      <td>44.0</td>
      <td>30.0</td>
      <td>2.80</td>
      <td>3.13</td>
      <td>66.0</td>
      <td>57.0</td>
      <td>0.25</td>
      <td>0.41</td>
      <td>33.0</td>
      <td>100.0</td>
      <td>60.0</td>
      <td>50.0</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>5163</th>
      <td>b675c94f20551631</td>
      <td>52cae54377b433b7</td>
      <td>ad047e3073a775f3</td>
      <td>Fighter A</td>
      <td>UFC 83: Serra vs St-Pierre 2</td>
      <td>2008-04-19</td>
      <td>Nate Quarry</td>
      <td>Kalib Starnes</td>
      <td>72.0</td>
      <td>75.0</td>
      <td>185.0</td>
      <td>185.0</td>
      <td>72.0</td>
      <td>74.0</td>
      <td>Open Stance</td>
      <td>Orthodox</td>
      <td>1972-03-18</td>
      <td>1975-01-06</td>
      <td>4.96</td>
      <td>2.71</td>
      <td>44.0</td>
      <td>48.0</td>
      <td>2.80</td>
      <td>4.43</td>
      <td>66.0</td>
      <td>53.0</td>
      <td>0.25</td>
      <td>1.46</td>
      <td>33.0</td>
      <td>33.0</td>
      <td>60.0</td>
      <td>25.0</td>
      <td>0.0</td>
      <td>0.6</td>
    </tr>
    <tr>
      <th>5821</th>
      <td>dd400f38c483f629</td>
      <td>59583ff832fe9d68</td>
      <td>c4b6099f0d25f75e</td>
      <td>Fighter A</td>
      <td>UFC 98: Evans vs Machida</td>
      <td>2009-05-23</td>
      <td>Krzysztof Soszynski</td>
      <td>Andre Gusmao</td>
      <td>73.0</td>
      <td>72.0</td>
      <td>205.0</td>
      <td>205.0</td>
      <td>77.0</td>
      <td>NaN</td>
      <td>Open Stance</td>
      <td>Orthodox</td>
      <td>1977-08-02</td>
      <td>1977-05-19</td>
      <td>3.37</td>
      <td>2.79</td>
      <td>39.0</td>
      <td>43.0</td>
      <td>3.13</td>
      <td>3.28</td>
      <td>58.0</td>
      <td>60.0</td>
      <td>0.52</td>
      <td>0.00</td>
      <td>25.0</td>
      <td>0.0</td>
      <td>71.0</td>
      <td>60.0</td>
      <td>1.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>6303</th>
      <td>ca3230227ee700b7</td>
      <td>52cae54377b433b7</td>
      <td>d3711d3784b76255</td>
      <td>Fighter A</td>
      <td>UFC 53: Heavy Hitters</td>
      <td>2005-06-04</td>
      <td>Nate Quarry</td>
      <td>Shonie Carter</td>
      <td>72.0</td>
      <td>70.0</td>
      <td>185.0</td>
      <td>170.0</td>
      <td>72.0</td>
      <td>NaN</td>
      <td>Open Stance</td>
      <td>Southpaw</td>
      <td>1972-03-18</td>
      <td>1972-05-03</td>
      <td>4.96</td>
      <td>1.62</td>
      <td>44.0</td>
      <td>36.0</td>
      <td>2.80</td>
      <td>2.32</td>
      <td>66.0</td>
      <td>47.0</td>
      <td>0.25</td>
      <td>0.75</td>
      <td>33.0</td>
      <td>66.0</td>
      <td>60.0</td>
      <td>75.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>7015</th>
      <td>edeba52fc9b2855f</td>
      <td>59583ff832fe9d68</td>
      <td>ea398c802d9998ee</td>
      <td>Fighter A</td>
      <td>The Ultimate Fighter: Team Nogueira vs. Team M...</td>
      <td>2008-12-13</td>
      <td>Krzysztof Soszynski</td>
      <td>Shane Primm</td>
      <td>73.0</td>
      <td>75.0</td>
      <td>205.0</td>
      <td>205.0</td>
      <td>77.0</td>
      <td>NaN</td>
      <td>Open Stance</td>
      <td>Orthodox</td>
      <td>1977-08-02</td>
      <td>1984-07-30</td>
      <td>3.37</td>
      <td>0.83</td>
      <td>39.0</td>
      <td>26.0</td>
      <td>3.13</td>
      <td>2.25</td>
      <td>58.0</td>
      <td>62.0</td>
      <td>0.52</td>
      <td>1.78</td>
      <td>25.0</td>
      <td>20.0</td>
      <td>71.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>7245</th>
      <td>b46679306287ea79</td>
      <td>52cae54377b433b7</td>
      <td>f341f9551ba744e2</td>
      <td>Fighter A</td>
      <td>UFC Fight Night: Thomas vs Florian</td>
      <td>2007-09-19</td>
      <td>Nate Quarry</td>
      <td>Pete Sell</td>
      <td>72.0</td>
      <td>71.0</td>
      <td>185.0</td>
      <td>170.0</td>
      <td>72.0</td>
      <td>75.0</td>
      <td>Open Stance</td>
      <td>Orthodox</td>
      <td>1972-03-18</td>
      <td>1982-08-05</td>
      <td>4.96</td>
      <td>2.60</td>
      <td>44.0</td>
      <td>42.0</td>
      <td>2.80</td>
      <td>3.65</td>
      <td>66.0</td>
      <td>51.0</td>
      <td>0.25</td>
      <td>0.91</td>
      <td>33.0</td>
      <td>21.0</td>
      <td>60.0</td>
      <td>47.0</td>
      <td>0.0</td>
      <td>0.9</td>
    </tr>
    <tr>
      <th>7423</th>
      <td>f434775e97920d95</td>
      <td>52cae54377b433b7</td>
      <td>fbbde91f7bc2d3c5</td>
      <td>Fighter A</td>
      <td>The Ultimate Fighter: Team Couture vs. Team Li...</td>
      <td>2005-04-09</td>
      <td>Nate Quarry</td>
      <td>Lodune Sincaid</td>
      <td>72.0</td>
      <td>69.0</td>
      <td>185.0</td>
      <td>185.0</td>
      <td>72.0</td>
      <td>NaN</td>
      <td>Open Stance</td>
      <td>Orthodox</td>
      <td>1972-03-18</td>
      <td>1973-05-07</td>
      <td>4.96</td>
      <td>2.72</td>
      <td>44.0</td>
      <td>42.0</td>
      <td>2.80</td>
      <td>6.17</td>
      <td>66.0</td>
      <td>57.0</td>
      <td>0.25</td>
      <td>0.00</td>
      <td>33.0</td>
      <td>0.0</td>
      <td>60.0</td>
      <td>75.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Now we have encoded the stance let encode the the winner column
# first lets have a look at the values
dataframe['winner'].value_counts()
```




    Fighter A     4842
    Fighter B     2535
    No Contest      84
    Draw            58
    Name: winner, dtype: int64



When looking at the target variable (the one we are trying to predict) we have discovered a few interesting things that we need to deal with. ...that our target variable, the winner column, is unbalanced!
This can be a big deal in machine learning. Let look at why! 

In our dataset the split on the target variable  is roughly 64% fighter a and 33% fighter b (with the remaining no contests and draws). If I train a model with this split the model might overfit to the overrepresented class, in this case fighter a.

This is particularly an issue because our model would show an accuracy around 64%, but the model will be incredibly fragile. Meaning that our model performance could suffer greatly in production! 

The most common way to deal with an unbalanced dataset is to resample our data. Resampling can be split into two main types:
1. Undersampling - 
2. Oversampling - 

Alternatively, we could create synthetic data using tools like SMOTE. However, in this case I will use oversampling of the minority class to solve the imbalance. We will deal with this problem later when we do our feature engineering but for now we will encode the winner column into numerical values to make it easier to work with when we train our model.

meaning the split in the classes are not equal. Because of some background knowledge of the UFC I know that the 'Red corner' fighter or Fighter_a in our data is the higher ranked fighter that it is common for them to win, so this could be an important feature to use, however, I want to base my model of the stats of a fighter and not use the corner as a feature. To do this I need to split the data so there is a 50/50 split between fighter A winning and fighter B. Luckily this is easy enough with our data set


```python
# Create encoder object
encoder = LabelEncoder()

# Fit and transform the 'winner' column
encoder.fit(dataframe['winner'])
dataframe['winner'] = encoder.transform(dataframe['winner'])

# check the values in the column
dataframe
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>fight_id</th>
      <th>fighter_a_id_FK</th>
      <th>event_id_FK</th>
      <th>winner</th>
      <th>name</th>
      <th>date</th>
      <th>fighter_a_name</th>
      <th>fighter_b_name</th>
      <th>fighter_a_height</th>
      <th>fighter_b_height</th>
      <th>fighter_a_weight</th>
      <th>fighter_b_weight</th>
      <th>fighter_a_reach</th>
      <th>fighter_b_reach</th>
      <th>fighter_a_stance</th>
      <th>fighter_b_stance</th>
      <th>fighter_a_dob</th>
      <th>fighter_b_dob</th>
      <th>fighter_a_SLpM</th>
      <th>fighter_b_SLpM</th>
      <th>fighter_a_str_acc</th>
      <th>fighter_b_str_acc</th>
      <th>fighter_a_SApM</th>
      <th>fighter_b_SApM</th>
      <th>fighter_a_str_def</th>
      <th>fighter_b_str_def</th>
      <th>fighter_a_TD_avg</th>
      <th>fighter_b_TD_avg</th>
      <th>fighter_a_TD_acc</th>
      <th>fighter_b_TD_acc</th>
      <th>fighter_a_TD_def</th>
      <th>fighter_b_TD_def</th>
      <th>fighter_a_sub_avg</th>
      <th>fighter_b_sub_avg</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1a191251620a84ca</td>
      <td>77d7295d1b22c694</td>
      <td>00a905a4a4a2b071</td>
      <td>2</td>
      <td>UFC Fight Night: Gane vs. Tuivasa</td>
      <td>2022-09-03</td>
      <td>Alessio Di Chirico</td>
      <td>Roman Kopylov</td>
      <td>72.0</td>
      <td>72.0</td>
      <td>185.0</td>
      <td>185.0</td>
      <td>74.0</td>
      <td>75.0</td>
      <td>Orthodox</td>
      <td>Southpaw</td>
      <td>1989-12-12</td>
      <td>1991-05-04</td>
      <td>3.32</td>
      <td>4.72</td>
      <td>39.0</td>
      <td>53.0</td>
      <td>3.44</td>
      <td>4.11</td>
      <td>61.0</td>
      <td>60.0</td>
      <td>1.46</td>
      <td>0.47</td>
      <td>39.0</td>
      <td>50.0</td>
      <td>83.0</td>
      <td>92.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>20f316f96c9e4458</td>
      <td>881bf86d4cba8578</td>
      <td>00a905a4a4a2b071</td>
      <td>1</td>
      <td>UFC Fight Night: Gane vs. Tuivasa</td>
      <td>2022-09-03</td>
      <td>Nassourdine Imavov</td>
      <td>Joaquin Buckley</td>
      <td>75.0</td>
      <td>70.0</td>
      <td>185.0</td>
      <td>170.0</td>
      <td>75.0</td>
      <td>76.0</td>
      <td>Orthodox</td>
      <td>Southpaw</td>
      <td>1995-03-01</td>
      <td>1994-04-27</td>
      <td>4.53</td>
      <td>3.87</td>
      <td>54.0</td>
      <td>33.0</td>
      <td>3.26</td>
      <td>3.31</td>
      <td>61.0</td>
      <td>57.0</td>
      <td>0.87</td>
      <td>1.51</td>
      <td>31.0</td>
      <td>36.0</td>
      <td>76.0</td>
      <td>65.0</td>
      <td>1.5</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>398db40015b3c81c</td>
      <td>e1d40e8782d80bc2</td>
      <td>00a905a4a4a2b071</td>
      <td>1</td>
      <td>UFC Fight Night: Gane vs. Tuivasa</td>
      <td>2022-09-03</td>
      <td>William Gomis</td>
      <td>Jarno Errens</td>
      <td>72.0</td>
      <td>71.0</td>
      <td>145.0</td>
      <td>145.0</td>
      <td>73.0</td>
      <td>73.0</td>
      <td>Southpaw</td>
      <td>Orthodox</td>
      <td>1997-06-13</td>
      <td>1994-11-17</td>
      <td>2.62</td>
      <td>1.67</td>
      <td>46.0</td>
      <td>38.0</td>
      <td>1.49</td>
      <td>2.93</td>
      <td>75.0</td>
      <td>52.0</td>
      <td>1.06</td>
      <td>0.00</td>
      <td>60.0</td>
      <td>0.0</td>
      <td>81.0</td>
      <td>33.0</td>
      <td>0.7</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3e2f00991f511607</td>
      <td>c2299ec916bc7c56</td>
      <td>00a905a4a4a2b071</td>
      <td>1</td>
      <td>UFC Fight Night: Gane vs. Tuivasa</td>
      <td>2022-09-03</td>
      <td>Benoit Saint Denis</td>
      <td>Gabriel Miranda</td>
      <td>71.0</td>
      <td>71.0</td>
      <td>155.0</td>
      <td>145.0</td>
      <td>73.0</td>
      <td>71.0</td>
      <td>Southpaw</td>
      <td>Orthodox</td>
      <td>1995-12-18</td>
      <td>1990-03-25</td>
      <td>5.53</td>
      <td>3.52</td>
      <td>52.0</td>
      <td>51.0</td>
      <td>5.20</td>
      <td>6.88</td>
      <td>44.0</td>
      <td>39.0</td>
      <td>4.55</td>
      <td>4.80</td>
      <td>36.0</td>
      <td>40.0</td>
      <td>66.0</td>
      <td>0.0</td>
      <td>1.4</td>
      <td>2.4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>7bced112f3229b1b</td>
      <td>c21a036b4e012f1c</td>
      <td>00a905a4a4a2b071</td>
      <td>2</td>
      <td>UFC Fight Night: Gane vs. Tuivasa</td>
      <td>2022-09-03</td>
      <td>John Makdessi</td>
      <td>Nasrat Haqparast</td>
      <td>68.0</td>
      <td>70.0</td>
      <td>155.0</td>
      <td>155.0</td>
      <td>68.0</td>
      <td>72.0</td>
      <td>Orthodox</td>
      <td>Southpaw</td>
      <td>1985-05-03</td>
      <td>1995-08-22</td>
      <td>5.52</td>
      <td>5.78</td>
      <td>49.0</td>
      <td>44.0</td>
      <td>4.15</td>
      <td>5.25</td>
      <td>68.0</td>
      <td>64.0</td>
      <td>0.00</td>
      <td>0.31</td>
      <td>0.0</td>
      <td>20.0</td>
      <td>86.0</td>
      <td>78.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>7514</th>
      <td>79817ab0129c5598</td>
      <td>634bb0de2eb043b4</td>
      <td>ff9578cdbfabd323</td>
      <td>1</td>
      <td>UFC 214: Cormier vs. Jones 2</td>
      <td>2017-07-29</td>
      <td>Cristiane Justino</td>
      <td>Tonya Evinger</td>
      <td>68.0</td>
      <td>67.0</td>
      <td>145.0</td>
      <td>135.0</td>
      <td>68.0</td>
      <td>70.0</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1985-07-09</td>
      <td>1981-06-04</td>
      <td>7.28</td>
      <td>1.16</td>
      <td>52.0</td>
      <td>42.0</td>
      <td>2.25</td>
      <td>4.02</td>
      <td>64.0</td>
      <td>38.0</td>
      <td>0.66</td>
      <td>0.93</td>
      <td>55.0</td>
      <td>30.0</td>
      <td>94.0</td>
      <td>33.0</td>
      <td>0.4</td>
      <td>0.9</td>
    </tr>
    <tr>
      <th>7515</th>
      <td>ac9e19a4075f6557</td>
      <td>cb696ebfb6598724</td>
      <td>ff9578cdbfabd323</td>
      <td>1</td>
      <td>UFC 214: Cormier vs. Jones 2</td>
      <td>2017-07-29</td>
      <td>Aljamain Sterling</td>
      <td>Renan Barao</td>
      <td>67.0</td>
      <td>66.0</td>
      <td>145.0</td>
      <td>145.0</td>
      <td>71.0</td>
      <td>70.0</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1989-07-31</td>
      <td>1987-01-31</td>
      <td>4.73</td>
      <td>3.78</td>
      <td>52.0</td>
      <td>36.0</td>
      <td>2.41</td>
      <td>3.90</td>
      <td>58.0</td>
      <td>57.0</td>
      <td>1.97</td>
      <td>1.49</td>
      <td>24.0</td>
      <td>41.0</td>
      <td>45.0</td>
      <td>91.0</td>
      <td>0.8</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>7516</th>
      <td>b8c967c742d3c107</td>
      <td>f2925e6db404bf1d</td>
      <td>ff9578cdbfabd323</td>
      <td>1</td>
      <td>UFC 214: Cormier vs. Jones 2</td>
      <td>2017-07-29</td>
      <td>Robbie Lawler</td>
      <td>Donald Cerrone</td>
      <td>71.0</td>
      <td>73.0</td>
      <td>170.0</td>
      <td>155.0</td>
      <td>74.0</td>
      <td>73.0</td>
      <td>Southpaw</td>
      <td>Orthodox</td>
      <td>1982-03-20</td>
      <td>1983-03-29</td>
      <td>3.85</td>
      <td>4.41</td>
      <td>47.0</td>
      <td>46.0</td>
      <td>4.67</td>
      <td>4.48</td>
      <td>59.0</td>
      <td>53.0</td>
      <td>0.65</td>
      <td>1.16</td>
      <td>64.0</td>
      <td>33.0</td>
      <td>65.0</td>
      <td>73.0</td>
      <td>0.0</td>
      <td>1.2</td>
    </tr>
    <tr>
      <th>7517</th>
      <td>dffad417175400ba</td>
      <td>3974fa35c917af1d</td>
      <td>ff9578cdbfabd323</td>
      <td>1</td>
      <td>UFC 214: Cormier vs. Jones 2</td>
      <td>2017-07-29</td>
      <td>Ricardo Lamas</td>
      <td>Jason Knight</td>
      <td>68.0</td>
      <td>70.0</td>
      <td>145.0</td>
      <td>145.0</td>
      <td>71.0</td>
      <td>71.0</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1982-05-21</td>
      <td>1992-07-14</td>
      <td>3.13</td>
      <td>2.95</td>
      <td>47.0</td>
      <td>33.0</td>
      <td>2.87</td>
      <td>3.02</td>
      <td>57.0</td>
      <td>51.0</td>
      <td>1.84</td>
      <td>1.31</td>
      <td>33.0</td>
      <td>31.0</td>
      <td>46.0</td>
      <td>47.0</td>
      <td>0.9</td>
      <td>1.7</td>
    </tr>
    <tr>
      <th>7518</th>
      <td>e80a8338d86e730b</td>
      <td>05b43e0ead3df345</td>
      <td>ff9578cdbfabd323</td>
      <td>2</td>
      <td>UFC 214: Cormier vs. Jones 2</td>
      <td>2017-07-29</td>
      <td>Kailin Curran</td>
      <td>Aleksandra Albu</td>
      <td>64.0</td>
      <td>62.0</td>
      <td>115.0</td>
      <td>115.0</td>
      <td>65.0</td>
      <td>63.0</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1991-04-11</td>
      <td>1990-07-14</td>
      <td>3.89</td>
      <td>4.83</td>
      <td>43.0</td>
      <td>47.0</td>
      <td>4.75</td>
      <td>5.56</td>
      <td>53.0</td>
      <td>38.0</td>
      <td>2.00</td>
      <td>2.27</td>
      <td>57.0</td>
      <td>50.0</td>
      <td>61.0</td>
      <td>77.0</td>
      <td>0.6</td>
      <td>0.4</td>
    </tr>
  </tbody>
</table>
<p>7519 rows × 34 columns</p>
</div>




```python
# Next lets drop the columns that we don't need 
columns_to_remove = ['fight_id', 'fighter_a_id_FK', 'event_id_FK', 'name', 'date', 'fighter_a_name', 
                    'fighter_b_name']

dataframe.drop(columns=columns_to_remove, axis=1, inplace=True)
dataframe
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>winner</th>
      <th>fighter_a_height</th>
      <th>fighter_b_height</th>
      <th>fighter_a_weight</th>
      <th>fighter_b_weight</th>
      <th>fighter_a_reach</th>
      <th>fighter_b_reach</th>
      <th>fighter_a_stance</th>
      <th>fighter_b_stance</th>
      <th>fighter_a_dob</th>
      <th>fighter_b_dob</th>
      <th>fighter_a_SLpM</th>
      <th>fighter_b_SLpM</th>
      <th>fighter_a_str_acc</th>
      <th>fighter_b_str_acc</th>
      <th>fighter_a_SApM</th>
      <th>fighter_b_SApM</th>
      <th>fighter_a_str_def</th>
      <th>fighter_b_str_def</th>
      <th>fighter_a_TD_avg</th>
      <th>fighter_b_TD_avg</th>
      <th>fighter_a_TD_acc</th>
      <th>fighter_b_TD_acc</th>
      <th>fighter_a_TD_def</th>
      <th>fighter_b_TD_def</th>
      <th>fighter_a_sub_avg</th>
      <th>fighter_b_sub_avg</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>72.0</td>
      <td>72.0</td>
      <td>185.0</td>
      <td>185.0</td>
      <td>74.0</td>
      <td>75.0</td>
      <td>Orthodox</td>
      <td>Southpaw</td>
      <td>1989-12-12</td>
      <td>1991-05-04</td>
      <td>3.32</td>
      <td>4.72</td>
      <td>39.0</td>
      <td>53.0</td>
      <td>3.44</td>
      <td>4.11</td>
      <td>61.0</td>
      <td>60.0</td>
      <td>1.46</td>
      <td>0.47</td>
      <td>39.0</td>
      <td>50.0</td>
      <td>83.0</td>
      <td>92.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>75.0</td>
      <td>70.0</td>
      <td>185.0</td>
      <td>170.0</td>
      <td>75.0</td>
      <td>76.0</td>
      <td>Orthodox</td>
      <td>Southpaw</td>
      <td>1995-03-01</td>
      <td>1994-04-27</td>
      <td>4.53</td>
      <td>3.87</td>
      <td>54.0</td>
      <td>33.0</td>
      <td>3.26</td>
      <td>3.31</td>
      <td>61.0</td>
      <td>57.0</td>
      <td>0.87</td>
      <td>1.51</td>
      <td>31.0</td>
      <td>36.0</td>
      <td>76.0</td>
      <td>65.0</td>
      <td>1.5</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>72.0</td>
      <td>71.0</td>
      <td>145.0</td>
      <td>145.0</td>
      <td>73.0</td>
      <td>73.0</td>
      <td>Southpaw</td>
      <td>Orthodox</td>
      <td>1997-06-13</td>
      <td>1994-11-17</td>
      <td>2.62</td>
      <td>1.67</td>
      <td>46.0</td>
      <td>38.0</td>
      <td>1.49</td>
      <td>2.93</td>
      <td>75.0</td>
      <td>52.0</td>
      <td>1.06</td>
      <td>0.00</td>
      <td>60.0</td>
      <td>0.0</td>
      <td>81.0</td>
      <td>33.0</td>
      <td>0.7</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>71.0</td>
      <td>71.0</td>
      <td>155.0</td>
      <td>145.0</td>
      <td>73.0</td>
      <td>71.0</td>
      <td>Southpaw</td>
      <td>Orthodox</td>
      <td>1995-12-18</td>
      <td>1990-03-25</td>
      <td>5.53</td>
      <td>3.52</td>
      <td>52.0</td>
      <td>51.0</td>
      <td>5.20</td>
      <td>6.88</td>
      <td>44.0</td>
      <td>39.0</td>
      <td>4.55</td>
      <td>4.80</td>
      <td>36.0</td>
      <td>40.0</td>
      <td>66.0</td>
      <td>0.0</td>
      <td>1.4</td>
      <td>2.4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>68.0</td>
      <td>70.0</td>
      <td>155.0</td>
      <td>155.0</td>
      <td>68.0</td>
      <td>72.0</td>
      <td>Orthodox</td>
      <td>Southpaw</td>
      <td>1985-05-03</td>
      <td>1995-08-22</td>
      <td>5.52</td>
      <td>5.78</td>
      <td>49.0</td>
      <td>44.0</td>
      <td>4.15</td>
      <td>5.25</td>
      <td>68.0</td>
      <td>64.0</td>
      <td>0.00</td>
      <td>0.31</td>
      <td>0.0</td>
      <td>20.0</td>
      <td>86.0</td>
      <td>78.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>7514</th>
      <td>1</td>
      <td>68.0</td>
      <td>67.0</td>
      <td>145.0</td>
      <td>135.0</td>
      <td>68.0</td>
      <td>70.0</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1985-07-09</td>
      <td>1981-06-04</td>
      <td>7.28</td>
      <td>1.16</td>
      <td>52.0</td>
      <td>42.0</td>
      <td>2.25</td>
      <td>4.02</td>
      <td>64.0</td>
      <td>38.0</td>
      <td>0.66</td>
      <td>0.93</td>
      <td>55.0</td>
      <td>30.0</td>
      <td>94.0</td>
      <td>33.0</td>
      <td>0.4</td>
      <td>0.9</td>
    </tr>
    <tr>
      <th>7515</th>
      <td>1</td>
      <td>67.0</td>
      <td>66.0</td>
      <td>145.0</td>
      <td>145.0</td>
      <td>71.0</td>
      <td>70.0</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1989-07-31</td>
      <td>1987-01-31</td>
      <td>4.73</td>
      <td>3.78</td>
      <td>52.0</td>
      <td>36.0</td>
      <td>2.41</td>
      <td>3.90</td>
      <td>58.0</td>
      <td>57.0</td>
      <td>1.97</td>
      <td>1.49</td>
      <td>24.0</td>
      <td>41.0</td>
      <td>45.0</td>
      <td>91.0</td>
      <td>0.8</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>7516</th>
      <td>1</td>
      <td>71.0</td>
      <td>73.0</td>
      <td>170.0</td>
      <td>155.0</td>
      <td>74.0</td>
      <td>73.0</td>
      <td>Southpaw</td>
      <td>Orthodox</td>
      <td>1982-03-20</td>
      <td>1983-03-29</td>
      <td>3.85</td>
      <td>4.41</td>
      <td>47.0</td>
      <td>46.0</td>
      <td>4.67</td>
      <td>4.48</td>
      <td>59.0</td>
      <td>53.0</td>
      <td>0.65</td>
      <td>1.16</td>
      <td>64.0</td>
      <td>33.0</td>
      <td>65.0</td>
      <td>73.0</td>
      <td>0.0</td>
      <td>1.2</td>
    </tr>
    <tr>
      <th>7517</th>
      <td>1</td>
      <td>68.0</td>
      <td>70.0</td>
      <td>145.0</td>
      <td>145.0</td>
      <td>71.0</td>
      <td>71.0</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1982-05-21</td>
      <td>1992-07-14</td>
      <td>3.13</td>
      <td>2.95</td>
      <td>47.0</td>
      <td>33.0</td>
      <td>2.87</td>
      <td>3.02</td>
      <td>57.0</td>
      <td>51.0</td>
      <td>1.84</td>
      <td>1.31</td>
      <td>33.0</td>
      <td>31.0</td>
      <td>46.0</td>
      <td>47.0</td>
      <td>0.9</td>
      <td>1.7</td>
    </tr>
    <tr>
      <th>7518</th>
      <td>2</td>
      <td>64.0</td>
      <td>62.0</td>
      <td>115.0</td>
      <td>115.0</td>
      <td>65.0</td>
      <td>63.0</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1991-04-11</td>
      <td>1990-07-14</td>
      <td>3.89</td>
      <td>4.83</td>
      <td>43.0</td>
      <td>47.0</td>
      <td>4.75</td>
      <td>5.56</td>
      <td>53.0</td>
      <td>38.0</td>
      <td>2.00</td>
      <td>2.27</td>
      <td>57.0</td>
      <td>50.0</td>
      <td>61.0</td>
      <td>77.0</td>
      <td>0.6</td>
      <td>0.4</td>
    </tr>
  </tbody>
</table>
<p>7519 rows × 27 columns</p>
</div>




```python
# Next lets encode with the stance of the fighters
stances = pd.concat([dataframe['fighter_a_stance'], dataframe['fighter_b_stance']]).unique()
stances = np.delete(stances, np.where(stances == None))
values = list(range(5))
replacement_dict = { k:v for (k,v) in zip(stances, values)} 

dataframe['fighter_a_stance'] = dataframe['fighter_a_stance'].replace(replacement_dict)
dataframe['fighter_b_stance'] = dataframe['fighter_b_stance'].replace(replacement_dict)
dataframe
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>winner</th>
      <th>fighter_a_height</th>
      <th>fighter_b_height</th>
      <th>fighter_a_weight</th>
      <th>fighter_b_weight</th>
      <th>fighter_a_reach</th>
      <th>fighter_b_reach</th>
      <th>fighter_a_stance</th>
      <th>fighter_b_stance</th>
      <th>fighter_a_dob</th>
      <th>fighter_b_dob</th>
      <th>fighter_a_SLpM</th>
      <th>fighter_b_SLpM</th>
      <th>fighter_a_str_acc</th>
      <th>fighter_b_str_acc</th>
      <th>fighter_a_SApM</th>
      <th>fighter_b_SApM</th>
      <th>fighter_a_str_def</th>
      <th>fighter_b_str_def</th>
      <th>fighter_a_TD_avg</th>
      <th>fighter_b_TD_avg</th>
      <th>fighter_a_TD_acc</th>
      <th>fighter_b_TD_acc</th>
      <th>fighter_a_TD_def</th>
      <th>fighter_b_TD_def</th>
      <th>fighter_a_sub_avg</th>
      <th>fighter_b_sub_avg</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>72.0</td>
      <td>72.0</td>
      <td>185.0</td>
      <td>185.0</td>
      <td>74.0</td>
      <td>75.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1989-12-12</td>
      <td>1991-05-04</td>
      <td>3.32</td>
      <td>4.72</td>
      <td>39.0</td>
      <td>53.0</td>
      <td>3.44</td>
      <td>4.11</td>
      <td>61.0</td>
      <td>60.0</td>
      <td>1.46</td>
      <td>0.47</td>
      <td>39.0</td>
      <td>50.0</td>
      <td>83.0</td>
      <td>92.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>75.0</td>
      <td>70.0</td>
      <td>185.0</td>
      <td>170.0</td>
      <td>75.0</td>
      <td>76.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1995-03-01</td>
      <td>1994-04-27</td>
      <td>4.53</td>
      <td>3.87</td>
      <td>54.0</td>
      <td>33.0</td>
      <td>3.26</td>
      <td>3.31</td>
      <td>61.0</td>
      <td>57.0</td>
      <td>0.87</td>
      <td>1.51</td>
      <td>31.0</td>
      <td>36.0</td>
      <td>76.0</td>
      <td>65.0</td>
      <td>1.5</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>72.0</td>
      <td>71.0</td>
      <td>145.0</td>
      <td>145.0</td>
      <td>73.0</td>
      <td>73.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1997-06-13</td>
      <td>1994-11-17</td>
      <td>2.62</td>
      <td>1.67</td>
      <td>46.0</td>
      <td>38.0</td>
      <td>1.49</td>
      <td>2.93</td>
      <td>75.0</td>
      <td>52.0</td>
      <td>1.06</td>
      <td>0.00</td>
      <td>60.0</td>
      <td>0.0</td>
      <td>81.0</td>
      <td>33.0</td>
      <td>0.7</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>71.0</td>
      <td>71.0</td>
      <td>155.0</td>
      <td>145.0</td>
      <td>73.0</td>
      <td>71.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1995-12-18</td>
      <td>1990-03-25</td>
      <td>5.53</td>
      <td>3.52</td>
      <td>52.0</td>
      <td>51.0</td>
      <td>5.20</td>
      <td>6.88</td>
      <td>44.0</td>
      <td>39.0</td>
      <td>4.55</td>
      <td>4.80</td>
      <td>36.0</td>
      <td>40.0</td>
      <td>66.0</td>
      <td>0.0</td>
      <td>1.4</td>
      <td>2.4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>68.0</td>
      <td>70.0</td>
      <td>155.0</td>
      <td>155.0</td>
      <td>68.0</td>
      <td>72.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1985-05-03</td>
      <td>1995-08-22</td>
      <td>5.52</td>
      <td>5.78</td>
      <td>49.0</td>
      <td>44.0</td>
      <td>4.15</td>
      <td>5.25</td>
      <td>68.0</td>
      <td>64.0</td>
      <td>0.00</td>
      <td>0.31</td>
      <td>0.0</td>
      <td>20.0</td>
      <td>86.0</td>
      <td>78.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>7514</th>
      <td>1</td>
      <td>68.0</td>
      <td>67.0</td>
      <td>145.0</td>
      <td>135.0</td>
      <td>68.0</td>
      <td>70.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1985-07-09</td>
      <td>1981-06-04</td>
      <td>7.28</td>
      <td>1.16</td>
      <td>52.0</td>
      <td>42.0</td>
      <td>2.25</td>
      <td>4.02</td>
      <td>64.0</td>
      <td>38.0</td>
      <td>0.66</td>
      <td>0.93</td>
      <td>55.0</td>
      <td>30.0</td>
      <td>94.0</td>
      <td>33.0</td>
      <td>0.4</td>
      <td>0.9</td>
    </tr>
    <tr>
      <th>7515</th>
      <td>1</td>
      <td>67.0</td>
      <td>66.0</td>
      <td>145.0</td>
      <td>145.0</td>
      <td>71.0</td>
      <td>70.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1989-07-31</td>
      <td>1987-01-31</td>
      <td>4.73</td>
      <td>3.78</td>
      <td>52.0</td>
      <td>36.0</td>
      <td>2.41</td>
      <td>3.90</td>
      <td>58.0</td>
      <td>57.0</td>
      <td>1.97</td>
      <td>1.49</td>
      <td>24.0</td>
      <td>41.0</td>
      <td>45.0</td>
      <td>91.0</td>
      <td>0.8</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>7516</th>
      <td>1</td>
      <td>71.0</td>
      <td>73.0</td>
      <td>170.0</td>
      <td>155.0</td>
      <td>74.0</td>
      <td>73.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1982-03-20</td>
      <td>1983-03-29</td>
      <td>3.85</td>
      <td>4.41</td>
      <td>47.0</td>
      <td>46.0</td>
      <td>4.67</td>
      <td>4.48</td>
      <td>59.0</td>
      <td>53.0</td>
      <td>0.65</td>
      <td>1.16</td>
      <td>64.0</td>
      <td>33.0</td>
      <td>65.0</td>
      <td>73.0</td>
      <td>0.0</td>
      <td>1.2</td>
    </tr>
    <tr>
      <th>7517</th>
      <td>1</td>
      <td>68.0</td>
      <td>70.0</td>
      <td>145.0</td>
      <td>145.0</td>
      <td>71.0</td>
      <td>71.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1982-05-21</td>
      <td>1992-07-14</td>
      <td>3.13</td>
      <td>2.95</td>
      <td>47.0</td>
      <td>33.0</td>
      <td>2.87</td>
      <td>3.02</td>
      <td>57.0</td>
      <td>51.0</td>
      <td>1.84</td>
      <td>1.31</td>
      <td>33.0</td>
      <td>31.0</td>
      <td>46.0</td>
      <td>47.0</td>
      <td>0.9</td>
      <td>1.7</td>
    </tr>
    <tr>
      <th>7518</th>
      <td>2</td>
      <td>64.0</td>
      <td>62.0</td>
      <td>115.0</td>
      <td>115.0</td>
      <td>65.0</td>
      <td>63.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1991-04-11</td>
      <td>1990-07-14</td>
      <td>3.89</td>
      <td>4.83</td>
      <td>43.0</td>
      <td>47.0</td>
      <td>4.75</td>
      <td>5.56</td>
      <td>53.0</td>
      <td>38.0</td>
      <td>2.00</td>
      <td>2.27</td>
      <td>57.0</td>
      <td>50.0</td>
      <td>61.0</td>
      <td>77.0</td>
      <td>0.6</td>
      <td>0.4</td>
    </tr>
  </tbody>
</table>
<p>7519 rows × 27 columns</p>
</div>




```python
plt.rcParams['figure.max_open_warning'] = 50
numerical_columns = list(dataframe.describe().columns)

# create a loop to make a boxplot and histogram for each point
for i in numerical_columns:
    plt.figure()
    plt.tight_layout()
    sns.set(rc={'figure.figsize':(8,5)})
    
    f, (ax_box, ax_hist) = plt.subplots(2, sharex=False)
    plt.gca().set(xlabel = i, ylabel = 'Frequency')
    sns.boxplot(data=dataframe, x=dataframe[i], ax=ax_box, linewidth=1.0)
    sns.histplot(data=dataframe, x=dataframe[i], ax=ax_hist, bins=10, kde=True)
```


    <Figure size 800x500 with 0 Axes>



    
![png](output_25_1.png)
    



    <Figure size 800x500 with 0 Axes>



    
![png](output_25_3.png)
    



    <Figure size 800x500 with 0 Axes>



    
![png](output_25_5.png)
    



    <Figure size 800x500 with 0 Axes>



    
![png](output_25_7.png)
    



    <Figure size 800x500 with 0 Axes>



    
![png](output_25_9.png)
    



    <Figure size 800x500 with 0 Axes>



    
![png](output_25_11.png)
    



    <Figure size 800x500 with 0 Axes>



    
![png](output_25_13.png)
    



    <Figure size 800x500 with 0 Axes>



    
![png](output_25_15.png)
    



    <Figure size 800x500 with 0 Axes>



    
![png](output_25_17.png)
    



    <Figure size 800x500 with 0 Axes>



    
![png](output_25_19.png)
    



    <Figure size 800x500 with 0 Axes>



    
![png](output_25_21.png)
    



    <Figure size 800x500 with 0 Axes>



    
![png](output_25_23.png)
    



    <Figure size 800x500 with 0 Axes>



    
![png](output_25_25.png)
    



    <Figure size 800x500 with 0 Axes>



    
![png](output_25_27.png)
    



    <Figure size 800x500 with 0 Axes>



    
![png](output_25_29.png)
    



    <Figure size 800x500 with 0 Axes>



    
![png](output_25_31.png)
    



    <Figure size 800x500 with 0 Axes>



    
![png](output_25_33.png)
    



    <Figure size 800x500 with 0 Axes>



    
![png](output_25_35.png)
    



    <Figure size 800x500 with 0 Axes>



    
![png](output_25_37.png)
    



    <Figure size 800x500 with 0 Axes>



    
![png](output_25_39.png)
    



    <Figure size 800x500 with 0 Axes>



    
![png](output_25_41.png)
    



    <Figure size 800x500 with 0 Axes>



    
![png](output_25_43.png)
    



    <Figure size 800x500 with 0 Axes>



    
![png](output_25_45.png)
    



    <Figure size 800x500 with 0 Axes>



    
![png](output_25_47.png)
    



    <Figure size 800x500 with 0 Axes>



    
![png](output_25_49.png)
    


Looking at the graphs above it seems that the data isn't overly skewed so using the mean to impute values seems like a good idea

With the exception of the weight column, some domain knowledge tells us it is a much better idea to impute the weight as the same as the opponent in the other column as fights occur at agreed upon weights (outside the heavyweight division) 


```python
# Next lets impute the missing values

# Now we have sorted the value types (excluding)

dataframe.describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>winner</th>
      <th>fighter_a_height</th>
      <th>fighter_b_height</th>
      <th>fighter_a_weight</th>
      <th>fighter_b_weight</th>
      <th>fighter_a_reach</th>
      <th>fighter_b_reach</th>
      <th>fighter_a_stance</th>
      <th>fighter_b_stance</th>
      <th>fighter_a_SLpM</th>
      <th>fighter_b_SLpM</th>
      <th>fighter_a_str_acc</th>
      <th>fighter_b_str_acc</th>
      <th>fighter_a_SApM</th>
      <th>fighter_b_SApM</th>
      <th>fighter_a_str_def</th>
      <th>fighter_b_str_def</th>
      <th>fighter_a_TD_avg</th>
      <th>fighter_b_TD_avg</th>
      <th>fighter_a_TD_acc</th>
      <th>fighter_b_TD_acc</th>
      <th>fighter_a_TD_def</th>
      <th>fighter_b_TD_def</th>
      <th>fighter_a_sub_avg</th>
      <th>fighter_b_sub_avg</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>7519.000000</td>
      <td>7514.000000</td>
      <td>7497.000000</td>
      <td>7516.000000</td>
      <td>7500.000000</td>
      <td>7098.000000</td>
      <td>6615.000000</td>
      <td>7493.000000</td>
      <td>7448.000000</td>
      <td>7519.000000</td>
      <td>7519.000000</td>
      <td>7519.000000</td>
      <td>7519.000000</td>
      <td>7519.000000</td>
      <td>7519.000000</td>
      <td>7519.000000</td>
      <td>7519.000000</td>
      <td>7519.000000</td>
      <td>7519.000000</td>
      <td>7519.000000</td>
      <td>7519.000000</td>
      <td>7519.000000</td>
      <td>7519.000000</td>
      <td>7519.000000</td>
      <td>7519.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>1.351776</td>
      <td>70.329518</td>
      <td>70.303988</td>
      <td>168.695317</td>
      <td>168.277867</td>
      <td>72.136940</td>
      <td>71.982162</td>
      <td>0.300681</td>
      <td>0.307599</td>
      <td>3.403030</td>
      <td>3.268089</td>
      <td>44.128475</td>
      <td>42.902248</td>
      <td>3.278053</td>
      <td>3.450549</td>
      <td>54.284080</td>
      <td>52.224764</td>
      <td>1.593326</td>
      <td>1.457615</td>
      <td>38.825509</td>
      <td>35.941748</td>
      <td>60.346323</td>
      <td>56.587844</td>
      <td>0.647626</td>
      <td>0.599668</td>
    </tr>
    <tr>
      <th>std</th>
      <td>0.515593</td>
      <td>3.527235</td>
      <td>3.497144</td>
      <td>36.198808</td>
      <td>36.796266</td>
      <td>4.262536</td>
      <td>4.186640</td>
      <td>0.564046</td>
      <td>0.578275</td>
      <td>1.312669</td>
      <td>1.457575</td>
      <td>9.254967</td>
      <td>11.055371</td>
      <td>1.220889</td>
      <td>1.551828</td>
      <td>9.825433</td>
      <td>11.683590</td>
      <td>1.265590</td>
      <td>1.302384</td>
      <td>18.653186</td>
      <td>21.348346</td>
      <td>20.544122</td>
      <td>23.970968</td>
      <td>0.792348</td>
      <td>0.807511</td>
    </tr>
    <tr>
      <th>min</th>
      <td>0.000000</td>
      <td>60.000000</td>
      <td>60.000000</td>
      <td>115.000000</td>
      <td>115.000000</td>
      <td>58.000000</td>
      <td>58.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>1.000000</td>
      <td>68.000000</td>
      <td>68.000000</td>
      <td>145.000000</td>
      <td>145.000000</td>
      <td>70.000000</td>
      <td>69.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>2.570000</td>
      <td>2.340000</td>
      <td>40.000000</td>
      <td>39.000000</td>
      <td>2.510000</td>
      <td>2.600000</td>
      <td>51.000000</td>
      <td>49.000000</td>
      <td>0.625000</td>
      <td>0.490000</td>
      <td>29.000000</td>
      <td>25.000000</td>
      <td>50.000000</td>
      <td>45.000000</td>
      <td>0.100000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>1.000000</td>
      <td>70.000000</td>
      <td>70.000000</td>
      <td>170.000000</td>
      <td>155.000000</td>
      <td>72.000000</td>
      <td>72.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>3.320000</td>
      <td>3.250000</td>
      <td>45.000000</td>
      <td>44.000000</td>
      <td>3.170000</td>
      <td>3.270000</td>
      <td>56.000000</td>
      <td>54.000000</td>
      <td>1.330000</td>
      <td>1.160000</td>
      <td>39.000000</td>
      <td>36.000000</td>
      <td>63.000000</td>
      <td>61.000000</td>
      <td>0.500000</td>
      <td>0.400000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2.000000</td>
      <td>73.000000</td>
      <td>73.000000</td>
      <td>185.000000</td>
      <td>185.000000</td>
      <td>75.000000</td>
      <td>75.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>4.190000</td>
      <td>4.110000</td>
      <td>50.000000</td>
      <td>49.000000</td>
      <td>3.930000</td>
      <td>4.170000</td>
      <td>60.000000</td>
      <td>59.000000</td>
      <td>2.290000</td>
      <td>2.110000</td>
      <td>50.000000</td>
      <td>48.000000</td>
      <td>74.000000</td>
      <td>72.000000</td>
      <td>0.900000</td>
      <td>0.800000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>3.000000</td>
      <td>83.000000</td>
      <td>83.000000</td>
      <td>345.000000</td>
      <td>770.000000</td>
      <td>84.000000</td>
      <td>84.000000</td>
      <td>4.000000</td>
      <td>4.000000</td>
      <td>10.220000</td>
      <td>12.150000</td>
      <td>83.000000</td>
      <td>100.000000</td>
      <td>15.480000</td>
      <td>42.000000</td>
      <td>84.000000</td>
      <td>100.000000</td>
      <td>11.110000</td>
      <td>13.950000</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>100.000000</td>
      <td>21.900000</td>
      <td>16.400000</td>
    </tr>
  </tbody>
</table>
</div>




```python
# We are going to feature engineer the fighters ages before we impute the values as the simple imputer
# I want to use from sklearn doesn't work with datetime objects
from datetime import date
# first lets gets todays date to calculate the fighters age
year = datetime.datetime.now().year

# Lets test on an example

# calculate the age of the fighter
#age = today.year - year
#print("Your age is",age,"years.")

# Lets turn this into a list comprehension to add the column
#dataframe['fighter_a_age'] = [birthdate.year - year for birthdate.year in dataframe['fighter_a_dob']]
#dataframe['fighter_a_age'] = [year - birthdate.year for birthdate in dataframe['fighter_a_dob'] if birthdate != None]
dataframe['fighter_a_age'] = [year - birthdate.year if birthdate is not None else None for birthdate in dataframe['fighter_a_dob']]
dataframe['fighter_b_age'] = [year - birthdate.year if birthdate is not None else None for birthdate in dataframe['fighter_b_dob']]

columns_to_remove = ['fighter_a_dob', 'fighter_b_dob']
dataframe.drop(columns=columns_to_remove, axis=1, inplace=True)

dataframe
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>winner</th>
      <th>fighter_a_height</th>
      <th>fighter_b_height</th>
      <th>fighter_a_weight</th>
      <th>fighter_b_weight</th>
      <th>fighter_a_reach</th>
      <th>fighter_b_reach</th>
      <th>fighter_a_stance</th>
      <th>fighter_b_stance</th>
      <th>fighter_a_SLpM</th>
      <th>fighter_b_SLpM</th>
      <th>fighter_a_str_acc</th>
      <th>fighter_b_str_acc</th>
      <th>fighter_a_SApM</th>
      <th>fighter_b_SApM</th>
      <th>fighter_a_str_def</th>
      <th>fighter_b_str_def</th>
      <th>fighter_a_TD_avg</th>
      <th>fighter_b_TD_avg</th>
      <th>fighter_a_TD_acc</th>
      <th>fighter_b_TD_acc</th>
      <th>fighter_a_TD_def</th>
      <th>fighter_b_TD_def</th>
      <th>fighter_a_sub_avg</th>
      <th>fighter_b_sub_avg</th>
      <th>fighter_a_age</th>
      <th>fighter_b_age</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>72.0</td>
      <td>72.0</td>
      <td>185.0</td>
      <td>185.0</td>
      <td>74.0</td>
      <td>75.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>3.32</td>
      <td>4.72</td>
      <td>39.0</td>
      <td>53.0</td>
      <td>3.44</td>
      <td>4.11</td>
      <td>61.0</td>
      <td>60.0</td>
      <td>1.46</td>
      <td>0.47</td>
      <td>39.0</td>
      <td>50.0</td>
      <td>83.0</td>
      <td>92.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>35.0</td>
      <td>33.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>75.0</td>
      <td>70.0</td>
      <td>185.0</td>
      <td>170.0</td>
      <td>75.0</td>
      <td>76.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>4.53</td>
      <td>3.87</td>
      <td>54.0</td>
      <td>33.0</td>
      <td>3.26</td>
      <td>3.31</td>
      <td>61.0</td>
      <td>57.0</td>
      <td>0.87</td>
      <td>1.51</td>
      <td>31.0</td>
      <td>36.0</td>
      <td>76.0</td>
      <td>65.0</td>
      <td>1.5</td>
      <td>0.0</td>
      <td>29.0</td>
      <td>30.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>72.0</td>
      <td>71.0</td>
      <td>145.0</td>
      <td>145.0</td>
      <td>73.0</td>
      <td>73.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>2.62</td>
      <td>1.67</td>
      <td>46.0</td>
      <td>38.0</td>
      <td>1.49</td>
      <td>2.93</td>
      <td>75.0</td>
      <td>52.0</td>
      <td>1.06</td>
      <td>0.00</td>
      <td>60.0</td>
      <td>0.0</td>
      <td>81.0</td>
      <td>33.0</td>
      <td>0.7</td>
      <td>0.5</td>
      <td>27.0</td>
      <td>30.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>71.0</td>
      <td>71.0</td>
      <td>155.0</td>
      <td>145.0</td>
      <td>73.0</td>
      <td>71.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>5.53</td>
      <td>3.52</td>
      <td>52.0</td>
      <td>51.0</td>
      <td>5.20</td>
      <td>6.88</td>
      <td>44.0</td>
      <td>39.0</td>
      <td>4.55</td>
      <td>4.80</td>
      <td>36.0</td>
      <td>40.0</td>
      <td>66.0</td>
      <td>0.0</td>
      <td>1.4</td>
      <td>2.4</td>
      <td>29.0</td>
      <td>34.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>68.0</td>
      <td>70.0</td>
      <td>155.0</td>
      <td>155.0</td>
      <td>68.0</td>
      <td>72.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>5.52</td>
      <td>5.78</td>
      <td>49.0</td>
      <td>44.0</td>
      <td>4.15</td>
      <td>5.25</td>
      <td>68.0</td>
      <td>64.0</td>
      <td>0.00</td>
      <td>0.31</td>
      <td>0.0</td>
      <td>20.0</td>
      <td>86.0</td>
      <td>78.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>39.0</td>
      <td>29.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>7514</th>
      <td>1</td>
      <td>68.0</td>
      <td>67.0</td>
      <td>145.0</td>
      <td>135.0</td>
      <td>68.0</td>
      <td>70.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>7.28</td>
      <td>1.16</td>
      <td>52.0</td>
      <td>42.0</td>
      <td>2.25</td>
      <td>4.02</td>
      <td>64.0</td>
      <td>38.0</td>
      <td>0.66</td>
      <td>0.93</td>
      <td>55.0</td>
      <td>30.0</td>
      <td>94.0</td>
      <td>33.0</td>
      <td>0.4</td>
      <td>0.9</td>
      <td>39.0</td>
      <td>43.0</td>
    </tr>
    <tr>
      <th>7515</th>
      <td>1</td>
      <td>67.0</td>
      <td>66.0</td>
      <td>145.0</td>
      <td>145.0</td>
      <td>71.0</td>
      <td>70.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>4.73</td>
      <td>3.78</td>
      <td>52.0</td>
      <td>36.0</td>
      <td>2.41</td>
      <td>3.90</td>
      <td>58.0</td>
      <td>57.0</td>
      <td>1.97</td>
      <td>1.49</td>
      <td>24.0</td>
      <td>41.0</td>
      <td>45.0</td>
      <td>91.0</td>
      <td>0.8</td>
      <td>0.5</td>
      <td>35.0</td>
      <td>37.0</td>
    </tr>
    <tr>
      <th>7516</th>
      <td>1</td>
      <td>71.0</td>
      <td>73.0</td>
      <td>170.0</td>
      <td>155.0</td>
      <td>74.0</td>
      <td>73.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>3.85</td>
      <td>4.41</td>
      <td>47.0</td>
      <td>46.0</td>
      <td>4.67</td>
      <td>4.48</td>
      <td>59.0</td>
      <td>53.0</td>
      <td>0.65</td>
      <td>1.16</td>
      <td>64.0</td>
      <td>33.0</td>
      <td>65.0</td>
      <td>73.0</td>
      <td>0.0</td>
      <td>1.2</td>
      <td>42.0</td>
      <td>41.0</td>
    </tr>
    <tr>
      <th>7517</th>
      <td>1</td>
      <td>68.0</td>
      <td>70.0</td>
      <td>145.0</td>
      <td>145.0</td>
      <td>71.0</td>
      <td>71.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>3.13</td>
      <td>2.95</td>
      <td>47.0</td>
      <td>33.0</td>
      <td>2.87</td>
      <td>3.02</td>
      <td>57.0</td>
      <td>51.0</td>
      <td>1.84</td>
      <td>1.31</td>
      <td>33.0</td>
      <td>31.0</td>
      <td>46.0</td>
      <td>47.0</td>
      <td>0.9</td>
      <td>1.7</td>
      <td>42.0</td>
      <td>32.0</td>
    </tr>
    <tr>
      <th>7518</th>
      <td>2</td>
      <td>64.0</td>
      <td>62.0</td>
      <td>115.0</td>
      <td>115.0</td>
      <td>65.0</td>
      <td>63.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>3.89</td>
      <td>4.83</td>
      <td>43.0</td>
      <td>47.0</td>
      <td>4.75</td>
      <td>5.56</td>
      <td>53.0</td>
      <td>38.0</td>
      <td>2.00</td>
      <td>2.27</td>
      <td>57.0</td>
      <td>50.0</td>
      <td>61.0</td>
      <td>77.0</td>
      <td>0.6</td>
      <td>0.4</td>
      <td>33.0</td>
      <td>34.0</td>
    </tr>
  </tbody>
</table>
<p>7519 rows × 27 columns</p>
</div>




```python
from sklearn.impute import SimpleImputer
# Assuming dataframe is a Pandas DataFrame with numeric columns containing NaN values
numeric_columns = dataframe.select_dtypes(include=[np.number]).columns

imp_mean = SimpleImputer(missing_values=np.nan, strategy='mean')
transformed_data = imp_mean.fit_transform(dataframe[numeric_columns])

dataframe[numeric_columns] = transformed_data

# Now if you want to check for NaN values in the DataFrame
print(dataframe.isna().sum())
```

    winner               0
    fighter_a_height     0
    fighter_b_height     0
    fighter_a_weight     0
    fighter_b_weight     0
    fighter_a_reach      0
    fighter_b_reach      0
    fighter_a_stance     0
    fighter_b_stance     0
    fighter_a_SLpM       0
    fighter_b_SLpM       0
    fighter_a_str_acc    0
    fighter_b_str_acc    0
    fighter_a_SApM       0
    fighter_b_SApM       0
    fighter_a_str_def    0
    fighter_b_str_def    0
    fighter_a_TD_avg     0
    fighter_b_TD_avg     0
    fighter_a_TD_acc     0
    fighter_b_TD_acc     0
    fighter_a_TD_def     0
    fighter_b_TD_def     0
    fighter_a_sub_avg    0
    fighter_b_sub_avg    0
    fighter_a_age        0
    fighter_b_age        0
    dtype: int64
    


```python
dataframe
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>winner</th>
      <th>fighter_a_height</th>
      <th>fighter_b_height</th>
      <th>fighter_a_weight</th>
      <th>fighter_b_weight</th>
      <th>fighter_a_reach</th>
      <th>fighter_b_reach</th>
      <th>fighter_a_stance</th>
      <th>fighter_b_stance</th>
      <th>fighter_a_SLpM</th>
      <th>fighter_b_SLpM</th>
      <th>fighter_a_str_acc</th>
      <th>fighter_b_str_acc</th>
      <th>fighter_a_SApM</th>
      <th>fighter_b_SApM</th>
      <th>fighter_a_str_def</th>
      <th>fighter_b_str_def</th>
      <th>fighter_a_TD_avg</th>
      <th>fighter_b_TD_avg</th>
      <th>fighter_a_TD_acc</th>
      <th>fighter_b_TD_acc</th>
      <th>fighter_a_TD_def</th>
      <th>fighter_b_TD_def</th>
      <th>fighter_a_sub_avg</th>
      <th>fighter_b_sub_avg</th>
      <th>fighter_a_age</th>
      <th>fighter_b_age</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2.0</td>
      <td>72.0</td>
      <td>72.0</td>
      <td>185.0</td>
      <td>185.0</td>
      <td>74.0</td>
      <td>75.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>3.32</td>
      <td>4.72</td>
      <td>39.0</td>
      <td>53.0</td>
      <td>3.44</td>
      <td>4.11</td>
      <td>61.0</td>
      <td>60.0</td>
      <td>1.46</td>
      <td>0.47</td>
      <td>39.0</td>
      <td>50.0</td>
      <td>83.0</td>
      <td>92.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>35.0</td>
      <td>33.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.0</td>
      <td>75.0</td>
      <td>70.0</td>
      <td>185.0</td>
      <td>170.0</td>
      <td>75.0</td>
      <td>76.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>4.53</td>
      <td>3.87</td>
      <td>54.0</td>
      <td>33.0</td>
      <td>3.26</td>
      <td>3.31</td>
      <td>61.0</td>
      <td>57.0</td>
      <td>0.87</td>
      <td>1.51</td>
      <td>31.0</td>
      <td>36.0</td>
      <td>76.0</td>
      <td>65.0</td>
      <td>1.5</td>
      <td>0.0</td>
      <td>29.0</td>
      <td>30.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1.0</td>
      <td>72.0</td>
      <td>71.0</td>
      <td>145.0</td>
      <td>145.0</td>
      <td>73.0</td>
      <td>73.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>2.62</td>
      <td>1.67</td>
      <td>46.0</td>
      <td>38.0</td>
      <td>1.49</td>
      <td>2.93</td>
      <td>75.0</td>
      <td>52.0</td>
      <td>1.06</td>
      <td>0.00</td>
      <td>60.0</td>
      <td>0.0</td>
      <td>81.0</td>
      <td>33.0</td>
      <td>0.7</td>
      <td>0.5</td>
      <td>27.0</td>
      <td>30.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1.0</td>
      <td>71.0</td>
      <td>71.0</td>
      <td>155.0</td>
      <td>145.0</td>
      <td>73.0</td>
      <td>71.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>5.53</td>
      <td>3.52</td>
      <td>52.0</td>
      <td>51.0</td>
      <td>5.20</td>
      <td>6.88</td>
      <td>44.0</td>
      <td>39.0</td>
      <td>4.55</td>
      <td>4.80</td>
      <td>36.0</td>
      <td>40.0</td>
      <td>66.0</td>
      <td>0.0</td>
      <td>1.4</td>
      <td>2.4</td>
      <td>29.0</td>
      <td>34.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2.0</td>
      <td>68.0</td>
      <td>70.0</td>
      <td>155.0</td>
      <td>155.0</td>
      <td>68.0</td>
      <td>72.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>5.52</td>
      <td>5.78</td>
      <td>49.0</td>
      <td>44.0</td>
      <td>4.15</td>
      <td>5.25</td>
      <td>68.0</td>
      <td>64.0</td>
      <td>0.00</td>
      <td>0.31</td>
      <td>0.0</td>
      <td>20.0</td>
      <td>86.0</td>
      <td>78.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>39.0</td>
      <td>29.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>7514</th>
      <td>1.0</td>
      <td>68.0</td>
      <td>67.0</td>
      <td>145.0</td>
      <td>135.0</td>
      <td>68.0</td>
      <td>70.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>7.28</td>
      <td>1.16</td>
      <td>52.0</td>
      <td>42.0</td>
      <td>2.25</td>
      <td>4.02</td>
      <td>64.0</td>
      <td>38.0</td>
      <td>0.66</td>
      <td>0.93</td>
      <td>55.0</td>
      <td>30.0</td>
      <td>94.0</td>
      <td>33.0</td>
      <td>0.4</td>
      <td>0.9</td>
      <td>39.0</td>
      <td>43.0</td>
    </tr>
    <tr>
      <th>7515</th>
      <td>1.0</td>
      <td>67.0</td>
      <td>66.0</td>
      <td>145.0</td>
      <td>145.0</td>
      <td>71.0</td>
      <td>70.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>4.73</td>
      <td>3.78</td>
      <td>52.0</td>
      <td>36.0</td>
      <td>2.41</td>
      <td>3.90</td>
      <td>58.0</td>
      <td>57.0</td>
      <td>1.97</td>
      <td>1.49</td>
      <td>24.0</td>
      <td>41.0</td>
      <td>45.0</td>
      <td>91.0</td>
      <td>0.8</td>
      <td>0.5</td>
      <td>35.0</td>
      <td>37.0</td>
    </tr>
    <tr>
      <th>7516</th>
      <td>1.0</td>
      <td>71.0</td>
      <td>73.0</td>
      <td>170.0</td>
      <td>155.0</td>
      <td>74.0</td>
      <td>73.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>3.85</td>
      <td>4.41</td>
      <td>47.0</td>
      <td>46.0</td>
      <td>4.67</td>
      <td>4.48</td>
      <td>59.0</td>
      <td>53.0</td>
      <td>0.65</td>
      <td>1.16</td>
      <td>64.0</td>
      <td>33.0</td>
      <td>65.0</td>
      <td>73.0</td>
      <td>0.0</td>
      <td>1.2</td>
      <td>42.0</td>
      <td>41.0</td>
    </tr>
    <tr>
      <th>7517</th>
      <td>1.0</td>
      <td>68.0</td>
      <td>70.0</td>
      <td>145.0</td>
      <td>145.0</td>
      <td>71.0</td>
      <td>71.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>3.13</td>
      <td>2.95</td>
      <td>47.0</td>
      <td>33.0</td>
      <td>2.87</td>
      <td>3.02</td>
      <td>57.0</td>
      <td>51.0</td>
      <td>1.84</td>
      <td>1.31</td>
      <td>33.0</td>
      <td>31.0</td>
      <td>46.0</td>
      <td>47.0</td>
      <td>0.9</td>
      <td>1.7</td>
      <td>42.0</td>
      <td>32.0</td>
    </tr>
    <tr>
      <th>7518</th>
      <td>2.0</td>
      <td>64.0</td>
      <td>62.0</td>
      <td>115.0</td>
      <td>115.0</td>
      <td>65.0</td>
      <td>63.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>3.89</td>
      <td>4.83</td>
      <td>43.0</td>
      <td>47.0</td>
      <td>4.75</td>
      <td>5.56</td>
      <td>53.0</td>
      <td>38.0</td>
      <td>2.00</td>
      <td>2.27</td>
      <td>57.0</td>
      <td>50.0</td>
      <td>61.0</td>
      <td>77.0</td>
      <td>0.6</td>
      <td>0.4</td>
      <td>33.0</td>
      <td>34.0</td>
    </tr>
  </tbody>
</table>
<p>7519 rows × 27 columns</p>
</div>




```python
# Drop rows where winner is 1 or 2 as this is a binary classification task and we don't have enough data 
# of draws and no contests to effective predict them
dataframe = dataframe.drop(dataframe[dataframe.winner == 3].index)
dataframe = dataframe.drop(dataframe[dataframe.winner == 0].index)
dataframe['winner'] = dataframe['winner'].replace(2.0, 0.0)
dataframe.winner.value_counts()
```




    1.0    4842
    0.0    2535
    Name: winner, dtype: int64




```python
# Now that our data is cleaned let start with a machine learning model before we
# do some feature engineering, then we can compare the effects of feature engineering.
import torch
import torch.nn as nn
import torch.optim as optim
from sklearn.model_selection import train_test_split

# Check if we are using GPU or CPU
device = "cuda" if torch.cuda.is_available() else "cpu"

# split our data into target and features
X = dataframe.drop('winner', axis=1)
y = dataframe['winner']

X = torch.tensor(X.values, dtype=torch.float32)
y = torch.tensor(y, dtype=torch.float32).reshape(-1)

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=13)
```


```python
# build a class for our model

class ufc_fighter_v0(nn.Module):
    def __init__(self):
        super().__init__()
        
        # create three layer neural network
        self.layer_1 = nn.Linear(in_features=26, out_features=52)
        self.layer_2 = nn.Linear(in_features=52, out_features=52)
        self.layer_3 = nn.Linear(in_features=52, out_features=1)
        self.relu = nn.ReLU()

    
    def forward(self, x):
        return self.layer_3(self.relu(self.layer_2(self.relu(self.layer_1(x)))))
    
```


```python
# instantiate our model
model_0 = ufc_fighter_v0().to(device)
model_0
```




    ufc_fighter_v0(
      (layer_1): Linear(in_features=26, out_features=52, bias=True)
      (layer_2): Linear(in_features=52, out_features=52, bias=True)
      (layer_3): Linear(in_features=52, out_features=1, bias=True)
      (relu): ReLU()
    )




```python
# Set up a loss function
loss_fn = nn.BCEWithLogitsLoss() # BCEWithLogitsLoss = sigmoid built-in

# Create an optimizer
optimizer = torch.optim.SGD(params=model_0.parameters(), 
                            lr=0.2)
```


```python
# Calculate accuracy (a classification metric)
def accuracy_fn(y_true, y_pred):
    correct = torch.eq(y_true, y_pred).sum().item() # torch.eq() calculates where two tensors are equal
    acc = (correct / len(y_pred)) * 100 
    return acc
```


```python
# Lets build our training loop

# number of epochs we want to run our training loop for
epochs = 200

# transfer our data onto the target device
X_train, y_train = X_train.to(device), y_train.to(device)
X_test, y_test = X_test.to(device), y_test.to(device)

# set up the training loop
for epoch in range(epochs):
    model_0.train()
    
    # forward pass
    y_logits = model_0(X_train).squeeze() # What is the purpose of squeeze?
    y_pred = torch.round(torch.sigmoid(y_logits))

    
    # calculate the loss/accuracy
    loss = loss_fn(y_logits, y_train)
    acc = accuracy_fn(y_true=y_train, y_pred=y_pred)
    
    # optimizer zero grad
    optimizer.zero_grad() # Why do we zero grad?
    
    # calculate the loss
    loss.backward()
    
    # optimizer
    optimizer.step()
    
    #  
    model_0.eval()
    with torch.inference_mode():
        # 1. Forward pass
        test_logits = model_0(X_test).squeeze() 
        test_pred = torch.round(torch.sigmoid(test_logits))
        # 2. Caculate loss/accuracy
        test_loss = loss_fn(test_logits,
                            y_test)
        test_acc = accuracy_fn(y_true=y_test,
                               y_pred=test_pred)

    # Print out what's happening every 10 epochs
    if epoch % 10 == 0:
        print(f"Epoch: {epoch} | Loss: {loss:.5f}, Accuracy: {acc:.2f}% | Test loss: {test_loss:.5f}, Test acc: {test_acc:.2f}%")
    
```

    Epoch: 0 | Loss: 0.64282, Accuracy: 65.72% | Test loss: 0.64553, Test acc: 65.31%
    Epoch: 10 | Loss: 0.64282, Accuracy: 65.72% | Test loss: 0.64553, Test acc: 65.31%
    Epoch: 20 | Loss: 0.64282, Accuracy: 65.72% | Test loss: 0.64553, Test acc: 65.31%
    Epoch: 30 | Loss: 0.64282, Accuracy: 65.72% | Test loss: 0.64553, Test acc: 65.31%
    Epoch: 40 | Loss: 0.64282, Accuracy: 65.72% | Test loss: 0.64553, Test acc: 65.31%
    Epoch: 50 | Loss: 0.64282, Accuracy: 65.72% | Test loss: 0.64553, Test acc: 65.31%
    Epoch: 60 | Loss: 0.64282, Accuracy: 65.72% | Test loss: 0.64553, Test acc: 65.31%
    Epoch: 70 | Loss: 0.64282, Accuracy: 65.72% | Test loss: 0.64553, Test acc: 65.31%
    Epoch: 80 | Loss: 0.64282, Accuracy: 65.72% | Test loss: 0.64553, Test acc: 65.31%
    Epoch: 90 | Loss: 0.64282, Accuracy: 65.72% | Test loss: 0.64553, Test acc: 65.31%
    Epoch: 100 | Loss: 0.64282, Accuracy: 65.72% | Test loss: 0.64553, Test acc: 65.31%
    Epoch: 110 | Loss: 0.64282, Accuracy: 65.72% | Test loss: 0.64553, Test acc: 65.31%
    Epoch: 120 | Loss: 0.64282, Accuracy: 65.72% | Test loss: 0.64553, Test acc: 65.31%
    Epoch: 130 | Loss: 0.64282, Accuracy: 65.72% | Test loss: 0.64553, Test acc: 65.31%
    Epoch: 140 | Loss: 0.64282, Accuracy: 65.72% | Test loss: 0.64553, Test acc: 65.31%
    Epoch: 150 | Loss: 0.64282, Accuracy: 65.72% | Test loss: 0.64553, Test acc: 65.31%
    Epoch: 160 | Loss: 0.64282, Accuracy: 65.72% | Test loss: 0.64553, Test acc: 65.31%
    Epoch: 170 | Loss: 0.64282, Accuracy: 65.72% | Test loss: 0.64553, Test acc: 65.31%
    Epoch: 180 | Loss: 0.64282, Accuracy: 65.72% | Test loss: 0.64553, Test acc: 65.31%
    Epoch: 190 | Loss: 0.64282, Accuracy: 65.72% | Test loss: 0.64553, Test acc: 65.31%
    


```python
# I have a suspicion that I didn't balance my dataset so lets look at the predicitons!
fighter_1 = 0
fighter_2 = 0
for pred in y_pred:
    if pred == 1:
        fighter_1 += 1
    else:
        fighter_2 += 1
print(f'Fighter 1: {fighter_1}, fighter 2: {fighter_2}')
```

    Fighter 1: 5901, fighter 2: 0
    

Now we have trained our first iteration of our model lets see if we can improve the performance with some feature engineering.

## Feature Engineering

Some of the features we think we can engineer from our data preprocessing steps include the following:
  1. Age from the DOB column
  2. Clustering to determine fighter styles
  3. Balance our target variable


```python
# Lets cluster our fighters - My assumption based on domain knowledge is we have 
# different style of fighters, which might help us predict the winner

from sklearn.cluster import KMeans

# split the features and target variable
y = dataframe['winner']
X = dataframe.drop('winner', axis=1)

# create a list of number of clusters to try
estimators = [
    ('K_means_cluster_3', KMeans(n_clusters=3)),
    ('K_means_cluster_5', KMeans(n_clusters=5)),
    ('K_means_cluster_7', KMeans(n_clusters=7)),
]

# create a plot 
fig = plt.figure(figsize=(10,8))
titles = ['3 Clusters', '5 Clusters', '7 Clusters']
for idx, ((name, est), title) in enumerate(zip(estimators, titles)):
    ax = fig.add_subplot(2, 2, idx + 1, projection='3d')
    est.fit(X)
    labels = est.labels_
    

```

    Index(['fighter_a_height', 'fighter_b_height', 'fighter_a_weight',
           'fighter_b_weight', 'fighter_a_reach', 'fighter_b_reach',
           'fighter_a_stance', 'fighter_b_stance', 'fighter_a_SLpM',
           'fighter_b_SLpM', 'fighter_a_str_acc', 'fighter_b_str_acc',
           'fighter_a_SApM', 'fighter_b_SApM', 'fighter_a_str_def',
           'fighter_b_str_def', 'fighter_a_TD_avg', 'fighter_b_TD_avg',
           'fighter_a_TD_acc', 'fighter_b_TD_acc', 'fighter_a_TD_def',
           'fighter_b_TD_def', 'fighter_a_sub_avg', 'fighter_b_sub_avg',
           'fighter_a_age', 'fighter_b_age'],
          dtype='object')
    


    
![png](output_41_1.png)
    



```python
# Lets look at how important each column is to the winner column
corr = dataframe.corr()

# plot the heatmap
sns.heatmap(corr, cmap="Blues")

```




    <AxesSubplot:>




    
![png](output_42_1.png)
    

