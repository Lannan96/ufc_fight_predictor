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
import numpy as np
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
      <td>0005e00b07cee542</td>
      <td>634e2fb70bde3fd5</td>
      <td>805ad1801eb26abb</td>
      <td>Fighter A</td>
      <td>UFC Fight Night: Holm vs. Aldana</td>
      <td>2020-10-03</td>
      <td>Holly Holm</td>
      <td>Irene Aldana</td>
      <td>68.0</td>
      <td>69.0</td>
      <td>135.0</td>
      <td>135.0</td>
      <td>69.0</td>
      <td>68.0</td>
      <td>Southpaw</td>
      <td>Orthodox</td>
      <td>1981-10-17</td>
      <td>1988-03-26</td>
      <td>3.21</td>
      <td>5.24</td>
      <td>40.0</td>
      <td>40.0</td>
      <td>2.79</td>
      <td>6.33</td>
      <td>56.0</td>
      <td>57.0</td>
      <td>0.90</td>
      <td>0.16</td>
      <td>30.0</td>
      <td>50.0</td>
      <td>78.0</td>
      <td>76.0</td>
      <td>0.1</td>
      <td>0.2</td>
    </tr>
    <tr>
      <th>1</th>
      <td>000da3152b7b5ab1</td>
      <td>6da99156486ed6c2</td>
      <td>f70144caea5c4c80</td>
      <td>Fighter A</td>
      <td>UFC 61: Bitter Rivals</td>
      <td>2006-07-08</td>
      <td>Joshua Burkman</td>
      <td>Josh Neer</td>
      <td>70.0</td>
      <td>71.0</td>
      <td>170.0</td>
      <td>170.0</td>
      <td>72.0</td>
      <td>72.0</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1980-04-10</td>
      <td>1983-03-24</td>
      <td>2.69</td>
      <td>3.29</td>
      <td>43.0</td>
      <td>46.0</td>
      <td>3.13</td>
      <td>3.63</td>
      <td>51.0</td>
      <td>58.0</td>
      <td>2.53</td>
      <td>1.09</td>
      <td>36.0</td>
      <td>34.0</td>
      <td>72.0</td>
      <td>46.0</td>
      <td>0.3</td>
      <td>1.3</td>
    </tr>
    <tr>
      <th>2</th>
      <td>001441f70c293931</td>
      <td>7826923b47f8d72a</td>
      <td>1d00756835ca67c9</td>
      <td>Fighter A</td>
      <td>UFC Fight Night: Volkov vs. Aspinall</td>
      <td>2022-03-19</td>
      <td>Paddy Pimblett</td>
      <td>Kazula Vargas</td>
      <td>70.0</td>
      <td>68.0</td>
      <td>155.0</td>
      <td>155.0</td>
      <td>73.0</td>
      <td>71.0</td>
      <td>Orthodox</td>
      <td>Southpaw</td>
      <td>1995-01-03</td>
      <td>1985-08-15</td>
      <td>5.13</td>
      <td>3.65</td>
      <td>52.0</td>
      <td>53.0</td>
      <td>3.70</td>
      <td>1.77</td>
      <td>41.0</td>
      <td>57.0</td>
      <td>0.98</td>
      <td>0.40</td>
      <td>25.0</td>
      <td>25.0</td>
      <td>56.0</td>
      <td>30.0</td>
      <td>1.6</td>
      <td>0.4</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0019ec81fd706ade</td>
      <td>85073dbd1be65ed9</td>
      <td>3ae10ac4df3df05c</td>
      <td>No Contest</td>
      <td>UFC Fight Night: Reyes vs. Weidman</td>
      <td>2019-10-18</td>
      <td>Greg Hardy</td>
      <td>Ben Sosoli</td>
      <td>77.0</td>
      <td>72.0</td>
      <td>265.0</td>
      <td>265.0</td>
      <td>80.0</td>
      <td>72.0</td>
      <td>Orthodox</td>
      <td>Southpaw</td>
      <td>1988-07-28</td>
      <td>1989-12-10</td>
      <td>4.79</td>
      <td>2.31</td>
      <td>50.0</td>
      <td>31.0</td>
      <td>3.31</td>
      <td>4.30</td>
      <td>55.0</td>
      <td>47.0</td>
      <td>0.20</td>
      <td>0.00</td>
      <td>33.0</td>
      <td>0.0</td>
      <td>64.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0027e179b743c86c</td>
      <td>91ea901c458e95dd</td>
      <td>f54200f1dfb9b5d4</td>
      <td>Fighter A</td>
      <td>UFC 185: Pettis vs Dos Anjos</td>
      <td>2015-03-14</td>
      <td>Jared Rosholt</td>
      <td>Josh Copeland</td>
      <td>74.0</td>
      <td>73.0</td>
      <td>265.0</td>
      <td>265.0</td>
      <td>75.0</td>
      <td>NaN</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1986-08-04</td>
      <td>1982-10-20</td>
      <td>2.08</td>
      <td>1.03</td>
      <td>46.0</td>
      <td>31.0</td>
      <td>1.52</td>
      <td>3.01</td>
      <td>59.0</td>
      <td>55.0</td>
      <td>1.83</td>
      <td>0.00</td>
      <td>41.0</td>
      <td>0.0</td>
      <td>66.0</td>
      <td>57.0</td>
      <td>0.1</td>
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
      <td>ffe4379d6bd1e82b</td>
      <td>2a542ee8a8b83559</td>
      <td>0cf935519d439ba6</td>
      <td>Fighter A</td>
      <td>UFC 39: The Warriors Return</td>
      <td>2002-09-27</td>
      <td>Tim Sylvia</td>
      <td>Wesley Correira</td>
      <td>80.0</td>
      <td>75.0</td>
      <td>265.0</td>
      <td>260.0</td>
      <td>80.0</td>
      <td>NaN</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1976-03-05</td>
      <td>1978-11-11</td>
      <td>4.23</td>
      <td>2.60</td>
      <td>41.0</td>
      <td>36.0</td>
      <td>2.61</td>
      <td>8.80</td>
      <td>61.0</td>
      <td>40.0</td>
      <td>0.11</td>
      <td>0.00</td>
      <td>100.0</td>
      <td>0.0</td>
      <td>75.0</td>
      <td>90.0</td>
      <td>0.1</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>7515</th>
      <td>ffe629a5232a878b</td>
      <td>08ae5cd9aef7ddd3</td>
      <td>108afe61a26bcbf4</td>
      <td>Fighter A</td>
      <td>UFC 43: Meltdown</td>
      <td>2003-06-06</td>
      <td>Kimo Leopoldo</td>
      <td>David Abbott</td>
      <td>75.0</td>
      <td>72.0</td>
      <td>235.0</td>
      <td>265.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Orthodox</td>
      <td>Switch</td>
      <td>1968-01-05</td>
      <td>None</td>
      <td>0.76</td>
      <td>1.35</td>
      <td>83.0</td>
      <td>30.0</td>
      <td>2.12</td>
      <td>3.55</td>
      <td>30.0</td>
      <td>38.0</td>
      <td>4.55</td>
      <td>1.07</td>
      <td>100.0</td>
      <td>33.0</td>
      <td>0.0</td>
      <td>66.0</td>
      <td>2.3</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>7516</th>
      <td>ffea776913451b6d</td>
      <td>22a92d7f62195791</td>
      <td>ad4e9055bf8cd04d</td>
      <td>Fighter A</td>
      <td>UFC 184: Rousey vs Zingano</td>
      <td>2015-02-28</td>
      <td>Tony Ferguson</td>
      <td>Gleison Tibau</td>
      <td>71.0</td>
      <td>70.0</td>
      <td>155.0</td>
      <td>155.0</td>
      <td>76.0</td>
      <td>71.0</td>
      <td>Orthodox</td>
      <td>Southpaw</td>
      <td>1984-02-12</td>
      <td>1983-10-07</td>
      <td>4.94</td>
      <td>1.95</td>
      <td>45.0</td>
      <td>31.0</td>
      <td>4.41</td>
      <td>2.51</td>
      <td>55.0</td>
      <td>63.0</td>
      <td>0.39</td>
      <td>4.08</td>
      <td>35.0</td>
      <td>53.0</td>
      <td>67.0</td>
      <td>92.0</td>
      <td>0.9</td>
      <td>0.8</td>
    </tr>
    <tr>
      <th>7517</th>
      <td>fffa21388cdd78b7</td>
      <td>c80095f6092271a7</td>
      <td>eae4aec1a5a8ff01</td>
      <td>Fighter A</td>
      <td>UFC 166: Velasquez vs Dos Santos 3</td>
      <td>2013-10-19</td>
      <td>Tim Boetsch</td>
      <td>CB Dollaway</td>
      <td>72.0</td>
      <td>74.0</td>
      <td>185.0</td>
      <td>185.0</td>
      <td>74.0</td>
      <td>76.0</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1981-01-28</td>
      <td>1983-08-10</td>
      <td>2.93</td>
      <td>2.65</td>
      <td>50.0</td>
      <td>47.0</td>
      <td>2.90</td>
      <td>2.58</td>
      <td>57.0</td>
      <td>54.0</td>
      <td>1.45</td>
      <td>3.55</td>
      <td>34.0</td>
      <td>54.0</td>
      <td>59.0</td>
      <td>62.0</td>
      <td>0.8</td>
      <td>1.2</td>
    </tr>
    <tr>
      <th>7518</th>
      <td>fffdc57255274be1</td>
      <td>2f5cbecbbe18bac4</td>
      <td>5717efc6f271cd52</td>
      <td>Fighter B</td>
      <td>UFC 283: Teixeira vs. Hill</td>
      <td>2023-01-21</td>
      <td>Shamil Abdurakhimov</td>
      <td>Jailton Almeida</td>
      <td>75.0</td>
      <td>75.0</td>
      <td>235.0</td>
      <td>205.0</td>
      <td>76.0</td>
      <td>79.0</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1981-09-02</td>
      <td>1991-06-26</td>
      <td>2.41</td>
      <td>2.78</td>
      <td>44.0</td>
      <td>64.0</td>
      <td>3.02</td>
      <td>0.52</td>
      <td>55.0</td>
      <td>43.0</td>
      <td>1.01</td>
      <td>5.14</td>
      <td>23.0</td>
      <td>55.0</td>
      <td>45.0</td>
      <td>75.0</td>
      <td>0.1</td>
      <td>2.4</td>
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
      <th>4</th>
      <td>0027e179b743c86c</td>
      <td>91ea901c458e95dd</td>
      <td>f54200f1dfb9b5d4</td>
      <td>Fighter A</td>
      <td>UFC 185: Pettis vs Dos Anjos</td>
      <td>2015-03-14</td>
      <td>Jared Rosholt</td>
      <td>Josh Copeland</td>
      <td>74.0</td>
      <td>73.0</td>
      <td>265.0</td>
      <td>265.0</td>
      <td>75.0</td>
      <td>NaN</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1986-08-04</td>
      <td>1982-10-20</td>
      <td>2.08</td>
      <td>1.03</td>
      <td>46.0</td>
      <td>31.0</td>
      <td>1.52</td>
      <td>3.01</td>
      <td>59.0</td>
      <td>55.0</td>
      <td>1.83</td>
      <td>0.00</td>
      <td>41.0</td>
      <td>0.0</td>
      <td>66.0</td>
      <td>57.0</td>
      <td>0.1</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>21</th>
      <td>0072df705378477c</td>
      <td>9b68bf67c5695185</td>
      <td>2f3f12002564bb55</td>
      <td>Fighter B</td>
      <td>UFC 117: Silva vs Sonnen</td>
      <td>2010-08-07</td>
      <td>Todd Brown</td>
      <td>Tim Boetsch</td>
      <td>71.0</td>
      <td>72.0</td>
      <td>205.0</td>
      <td>185.0</td>
      <td>NaN</td>
      <td>74.0</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1976-12-13</td>
      <td>1981-01-28</td>
      <td>2.15</td>
      <td>2.93</td>
      <td>45.0</td>
      <td>50.0</td>
      <td>3.85</td>
      <td>2.90</td>
      <td>48.0</td>
      <td>57.0</td>
      <td>0.00</td>
      <td>1.45</td>
      <td>0.0</td>
      <td>34.0</td>
      <td>50.0</td>
      <td>59.0</td>
      <td>0.0</td>
      <td>0.8</td>
    </tr>
    <tr>
      <th>22</th>
      <td>00731068c3195f7f</td>
      <td>22aa91e402d0fe1f</td>
      <td>505934897b8b4824</td>
      <td>Fighter A</td>
      <td>UFC on FX: Maynard vs Guida</td>
      <td>2012-06-22</td>
      <td>Ramsey Nijem</td>
      <td>CJ Keith</td>
      <td>71.0</td>
      <td>72.0</td>
      <td>155.0</td>
      <td>155.0</td>
      <td>75.0</td>
      <td>NaN</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1988-04-01</td>
      <td>1986-08-09</td>
      <td>3.05</td>
      <td>0.86</td>
      <td>44.0</td>
      <td>50.0</td>
      <td>1.62</td>
      <td>1.82</td>
      <td>62.0</td>
      <td>50.0</td>
      <td>5.32</td>
      <td>0.00</td>
      <td>62.0</td>
      <td>0.0</td>
      <td>55.0</td>
      <td>62.0</td>
      <td>1.1</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>24</th>
      <td>00835554f95fa911</td>
      <td>429e7d3725852ce9</td>
      <td>a6a9ab5a824e8f66</td>
      <td>Fighter A</td>
      <td>UFC 2: No Way Out</td>
      <td>1994-03-11</td>
      <td>Royce Gracie</td>
      <td>Patrick Smith</td>
      <td>73.0</td>
      <td>74.0</td>
      <td>175.0</td>
      <td>225.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Southpaw</td>
      <td>Orthodox</td>
      <td>1966-12-12</td>
      <td>1963-08-28</td>
      <td>0.88</td>
      <td>0.00</td>
      <td>41.0</td>
      <td>0.0</td>
      <td>1.13</td>
      <td>0.00</td>
      <td>37.0</td>
      <td>0.0</td>
      <td>0.00</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>66.0</td>
      <td>0.0</td>
      <td>0.8</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>25</th>
      <td>008d0158831dcbd4</td>
      <td>1fcfc3709fe58151</td>
      <td>194fc025f9355db6</td>
      <td>Fighter A</td>
      <td>UFC Fight Night: Jedrzejczyk vs Penne</td>
      <td>2015-06-20</td>
      <td>Peter Sobotta</td>
      <td>Steve Kennedy</td>
      <td>72.0</td>
      <td>71.0</td>
      <td>170.0</td>
      <td>170.0</td>
      <td>75.0</td>
      <td>NaN</td>
      <td>Southpaw</td>
      <td>Orthodox</td>
      <td>1987-01-11</td>
      <td>1983-03-07</td>
      <td>2.14</td>
      <td>2.28</td>
      <td>40.0</td>
      <td>35.0</td>
      <td>2.90</td>
      <td>5.52</td>
      <td>58.0</td>
      <td>45.0</td>
      <td>1.53</td>
      <td>2.51</td>
      <td>32.0</td>
      <td>33.0</td>
      <td>77.0</td>
      <td>0.0</td>
      <td>0.5</td>
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
      <th>7486</th>
      <td>fea1931b041cc5ff</td>
      <td>6aa1cbc1466e9a0b</td>
      <td>271fe91f4ba9d2c5</td>
      <td>Fighter A</td>
      <td>UFC 55: Fury</td>
      <td>2005-10-07</td>
      <td>Marcio Cruz</td>
      <td>Keigo Kunihara</td>
      <td>76.0</td>
      <td>72.0</td>
      <td>232.0</td>
      <td>235.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1978-04-24</td>
      <td>None</td>
      <td>2.92</td>
      <td>0.17</td>
      <td>46.0</td>
      <td>10.0</td>
      <td>1.51</td>
      <td>1.99</td>
      <td>68.0</td>
      <td>57.0</td>
      <td>0.53</td>
      <td>4.97</td>
      <td>7.0</td>
      <td>40.0</td>
      <td>37.0</td>
      <td>100.0</td>
      <td>1.6</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>7495</th>
      <td>fee1d48d7bc17e56</td>
      <td>a8e6a69796280f17</td>
      <td>577ec7e108b94be3</td>
      <td>Fighter A</td>
      <td>Ortiz vs Shamrock 3: The Final Chapter</td>
      <td>2006-10-10</td>
      <td>Nate Marquardt</td>
      <td>Crafton Wallace</td>
      <td>72.0</td>
      <td>72.0</td>
      <td>185.0</td>
      <td>185.0</td>
      <td>74.0</td>
      <td>NaN</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1979-04-20</td>
      <td>1975-11-10</td>
      <td>2.71</td>
      <td>1.55</td>
      <td>49.0</td>
      <td>44.0</td>
      <td>2.32</td>
      <td>2.36</td>
      <td>55.0</td>
      <td>47.0</td>
      <td>1.87</td>
      <td>0.00</td>
      <td>51.0</td>
      <td>0.0</td>
      <td>70.0</td>
      <td>42.0</td>
      <td>0.8</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>7509</th>
      <td>ff8802aae3b81c3f</td>
      <td>75e5fec9f72910ef</td>
      <td>304fcd812f12c589</td>
      <td>Fighter A</td>
      <td>UFC Fight Night: Stout vs Fisher</td>
      <td>2007-06-12</td>
      <td>Gleison Tibau</td>
      <td>Jeff Cox</td>
      <td>70.0</td>
      <td>70.0</td>
      <td>155.0</td>
      <td>155.0</td>
      <td>71.0</td>
      <td>NaN</td>
      <td>Southpaw</td>
      <td>Orthodox</td>
      <td>1983-10-07</td>
      <td>1968-08-02</td>
      <td>1.95</td>
      <td>0.56</td>
      <td>31.0</td>
      <td>25.0</td>
      <td>2.51</td>
      <td>1.69</td>
      <td>63.0</td>
      <td>33.0</td>
      <td>4.08</td>
      <td>4.23</td>
      <td>53.0</td>
      <td>50.0</td>
      <td>92.0</td>
      <td>80.0</td>
      <td>0.8</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>7514</th>
      <td>ffe4379d6bd1e82b</td>
      <td>2a542ee8a8b83559</td>
      <td>0cf935519d439ba6</td>
      <td>Fighter A</td>
      <td>UFC 39: The Warriors Return</td>
      <td>2002-09-27</td>
      <td>Tim Sylvia</td>
      <td>Wesley Correira</td>
      <td>80.0</td>
      <td>75.0</td>
      <td>265.0</td>
      <td>260.0</td>
      <td>80.0</td>
      <td>NaN</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1976-03-05</td>
      <td>1978-11-11</td>
      <td>4.23</td>
      <td>2.60</td>
      <td>41.0</td>
      <td>36.0</td>
      <td>2.61</td>
      <td>8.80</td>
      <td>61.0</td>
      <td>40.0</td>
      <td>0.11</td>
      <td>0.00</td>
      <td>100.0</td>
      <td>0.0</td>
      <td>75.0</td>
      <td>90.0</td>
      <td>0.1</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>7515</th>
      <td>ffe629a5232a878b</td>
      <td>08ae5cd9aef7ddd3</td>
      <td>108afe61a26bcbf4</td>
      <td>Fighter A</td>
      <td>UFC 43: Meltdown</td>
      <td>2003-06-06</td>
      <td>Kimo Leopoldo</td>
      <td>David Abbott</td>
      <td>75.0</td>
      <td>72.0</td>
      <td>235.0</td>
      <td>265.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Orthodox</td>
      <td>Switch</td>
      <td>1968-01-05</td>
      <td>None</td>
      <td>0.76</td>
      <td>1.35</td>
      <td>83.0</td>
      <td>30.0</td>
      <td>2.12</td>
      <td>3.55</td>
      <td>30.0</td>
      <td>38.0</td>
      <td>4.55</td>
      <td>1.07</td>
      <td>100.0</td>
      <td>33.0</td>
      <td>0.0</td>
      <td>66.0</td>
      <td>2.3</td>
      <td>0.0</td>
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
      <th>456</th>
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
      <th>967</th>
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
      <th>2466</th>
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
      <th>2530</th>
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
      <th>2861</th>
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
      <th>3617</th>
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
      <th>5321</th>
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
      <th>5362</th>
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
      <th>5388</th>
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
      <th>5633</th>
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
      <th>5981</th>
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
      <th>6493</th>
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
      <th>6577</th>
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
      <th>7001</th>
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
      <th>7181</th>
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
      <td>0005e00b07cee542</td>
      <td>634e2fb70bde3fd5</td>
      <td>805ad1801eb26abb</td>
      <td>1</td>
      <td>UFC Fight Night: Holm vs. Aldana</td>
      <td>2020-10-03</td>
      <td>Holly Holm</td>
      <td>Irene Aldana</td>
      <td>68.0</td>
      <td>69.0</td>
      <td>135.0</td>
      <td>135.0</td>
      <td>69.0</td>
      <td>68.0</td>
      <td>Southpaw</td>
      <td>Orthodox</td>
      <td>1981-10-17</td>
      <td>1988-03-26</td>
      <td>3.21</td>
      <td>5.24</td>
      <td>40.0</td>
      <td>40.0</td>
      <td>2.79</td>
      <td>6.33</td>
      <td>56.0</td>
      <td>57.0</td>
      <td>0.90</td>
      <td>0.16</td>
      <td>30.0</td>
      <td>50.0</td>
      <td>78.0</td>
      <td>76.0</td>
      <td>0.1</td>
      <td>0.2</td>
    </tr>
    <tr>
      <th>1</th>
      <td>000da3152b7b5ab1</td>
      <td>6da99156486ed6c2</td>
      <td>f70144caea5c4c80</td>
      <td>1</td>
      <td>UFC 61: Bitter Rivals</td>
      <td>2006-07-08</td>
      <td>Joshua Burkman</td>
      <td>Josh Neer</td>
      <td>70.0</td>
      <td>71.0</td>
      <td>170.0</td>
      <td>170.0</td>
      <td>72.0</td>
      <td>72.0</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1980-04-10</td>
      <td>1983-03-24</td>
      <td>2.69</td>
      <td>3.29</td>
      <td>43.0</td>
      <td>46.0</td>
      <td>3.13</td>
      <td>3.63</td>
      <td>51.0</td>
      <td>58.0</td>
      <td>2.53</td>
      <td>1.09</td>
      <td>36.0</td>
      <td>34.0</td>
      <td>72.0</td>
      <td>46.0</td>
      <td>0.3</td>
      <td>1.3</td>
    </tr>
    <tr>
      <th>2</th>
      <td>001441f70c293931</td>
      <td>7826923b47f8d72a</td>
      <td>1d00756835ca67c9</td>
      <td>1</td>
      <td>UFC Fight Night: Volkov vs. Aspinall</td>
      <td>2022-03-19</td>
      <td>Paddy Pimblett</td>
      <td>Kazula Vargas</td>
      <td>70.0</td>
      <td>68.0</td>
      <td>155.0</td>
      <td>155.0</td>
      <td>73.0</td>
      <td>71.0</td>
      <td>Orthodox</td>
      <td>Southpaw</td>
      <td>1995-01-03</td>
      <td>1985-08-15</td>
      <td>5.13</td>
      <td>3.65</td>
      <td>52.0</td>
      <td>53.0</td>
      <td>3.70</td>
      <td>1.77</td>
      <td>41.0</td>
      <td>57.0</td>
      <td>0.98</td>
      <td>0.40</td>
      <td>25.0</td>
      <td>25.0</td>
      <td>56.0</td>
      <td>30.0</td>
      <td>1.6</td>
      <td>0.4</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0019ec81fd706ade</td>
      <td>85073dbd1be65ed9</td>
      <td>3ae10ac4df3df05c</td>
      <td>3</td>
      <td>UFC Fight Night: Reyes vs. Weidman</td>
      <td>2019-10-18</td>
      <td>Greg Hardy</td>
      <td>Ben Sosoli</td>
      <td>77.0</td>
      <td>72.0</td>
      <td>265.0</td>
      <td>265.0</td>
      <td>80.0</td>
      <td>72.0</td>
      <td>Orthodox</td>
      <td>Southpaw</td>
      <td>1988-07-28</td>
      <td>1989-12-10</td>
      <td>4.79</td>
      <td>2.31</td>
      <td>50.0</td>
      <td>31.0</td>
      <td>3.31</td>
      <td>4.30</td>
      <td>55.0</td>
      <td>47.0</td>
      <td>0.20</td>
      <td>0.00</td>
      <td>33.0</td>
      <td>0.0</td>
      <td>64.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0027e179b743c86c</td>
      <td>91ea901c458e95dd</td>
      <td>f54200f1dfb9b5d4</td>
      <td>1</td>
      <td>UFC 185: Pettis vs Dos Anjos</td>
      <td>2015-03-14</td>
      <td>Jared Rosholt</td>
      <td>Josh Copeland</td>
      <td>74.0</td>
      <td>73.0</td>
      <td>265.0</td>
      <td>265.0</td>
      <td>75.0</td>
      <td>NaN</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1986-08-04</td>
      <td>1982-10-20</td>
      <td>2.08</td>
      <td>1.03</td>
      <td>46.0</td>
      <td>31.0</td>
      <td>1.52</td>
      <td>3.01</td>
      <td>59.0</td>
      <td>55.0</td>
      <td>1.83</td>
      <td>0.00</td>
      <td>41.0</td>
      <td>0.0</td>
      <td>66.0</td>
      <td>57.0</td>
      <td>0.1</td>
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
      <td>ffe4379d6bd1e82b</td>
      <td>2a542ee8a8b83559</td>
      <td>0cf935519d439ba6</td>
      <td>1</td>
      <td>UFC 39: The Warriors Return</td>
      <td>2002-09-27</td>
      <td>Tim Sylvia</td>
      <td>Wesley Correira</td>
      <td>80.0</td>
      <td>75.0</td>
      <td>265.0</td>
      <td>260.0</td>
      <td>80.0</td>
      <td>NaN</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1976-03-05</td>
      <td>1978-11-11</td>
      <td>4.23</td>
      <td>2.60</td>
      <td>41.0</td>
      <td>36.0</td>
      <td>2.61</td>
      <td>8.80</td>
      <td>61.0</td>
      <td>40.0</td>
      <td>0.11</td>
      <td>0.00</td>
      <td>100.0</td>
      <td>0.0</td>
      <td>75.0</td>
      <td>90.0</td>
      <td>0.1</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>7515</th>
      <td>ffe629a5232a878b</td>
      <td>08ae5cd9aef7ddd3</td>
      <td>108afe61a26bcbf4</td>
      <td>1</td>
      <td>UFC 43: Meltdown</td>
      <td>2003-06-06</td>
      <td>Kimo Leopoldo</td>
      <td>David Abbott</td>
      <td>75.0</td>
      <td>72.0</td>
      <td>235.0</td>
      <td>265.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Orthodox</td>
      <td>Switch</td>
      <td>1968-01-05</td>
      <td>None</td>
      <td>0.76</td>
      <td>1.35</td>
      <td>83.0</td>
      <td>30.0</td>
      <td>2.12</td>
      <td>3.55</td>
      <td>30.0</td>
      <td>38.0</td>
      <td>4.55</td>
      <td>1.07</td>
      <td>100.0</td>
      <td>33.0</td>
      <td>0.0</td>
      <td>66.0</td>
      <td>2.3</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>7516</th>
      <td>ffea776913451b6d</td>
      <td>22a92d7f62195791</td>
      <td>ad4e9055bf8cd04d</td>
      <td>1</td>
      <td>UFC 184: Rousey vs Zingano</td>
      <td>2015-02-28</td>
      <td>Tony Ferguson</td>
      <td>Gleison Tibau</td>
      <td>71.0</td>
      <td>70.0</td>
      <td>155.0</td>
      <td>155.0</td>
      <td>76.0</td>
      <td>71.0</td>
      <td>Orthodox</td>
      <td>Southpaw</td>
      <td>1984-02-12</td>
      <td>1983-10-07</td>
      <td>4.94</td>
      <td>1.95</td>
      <td>45.0</td>
      <td>31.0</td>
      <td>4.41</td>
      <td>2.51</td>
      <td>55.0</td>
      <td>63.0</td>
      <td>0.39</td>
      <td>4.08</td>
      <td>35.0</td>
      <td>53.0</td>
      <td>67.0</td>
      <td>92.0</td>
      <td>0.9</td>
      <td>0.8</td>
    </tr>
    <tr>
      <th>7517</th>
      <td>fffa21388cdd78b7</td>
      <td>c80095f6092271a7</td>
      <td>eae4aec1a5a8ff01</td>
      <td>1</td>
      <td>UFC 166: Velasquez vs Dos Santos 3</td>
      <td>2013-10-19</td>
      <td>Tim Boetsch</td>
      <td>CB Dollaway</td>
      <td>72.0</td>
      <td>74.0</td>
      <td>185.0</td>
      <td>185.0</td>
      <td>74.0</td>
      <td>76.0</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1981-01-28</td>
      <td>1983-08-10</td>
      <td>2.93</td>
      <td>2.65</td>
      <td>50.0</td>
      <td>47.0</td>
      <td>2.90</td>
      <td>2.58</td>
      <td>57.0</td>
      <td>54.0</td>
      <td>1.45</td>
      <td>3.55</td>
      <td>34.0</td>
      <td>54.0</td>
      <td>59.0</td>
      <td>62.0</td>
      <td>0.8</td>
      <td>1.2</td>
    </tr>
    <tr>
      <th>7518</th>
      <td>fffdc57255274be1</td>
      <td>2f5cbecbbe18bac4</td>
      <td>5717efc6f271cd52</td>
      <td>2</td>
      <td>UFC 283: Teixeira vs. Hill</td>
      <td>2023-01-21</td>
      <td>Shamil Abdurakhimov</td>
      <td>Jailton Almeida</td>
      <td>75.0</td>
      <td>75.0</td>
      <td>235.0</td>
      <td>205.0</td>
      <td>76.0</td>
      <td>79.0</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1981-09-02</td>
      <td>1991-06-26</td>
      <td>2.41</td>
      <td>2.78</td>
      <td>44.0</td>
      <td>64.0</td>
      <td>3.02</td>
      <td>0.52</td>
      <td>55.0</td>
      <td>43.0</td>
      <td>1.01</td>
      <td>5.14</td>
      <td>23.0</td>
      <td>55.0</td>
      <td>45.0</td>
      <td>75.0</td>
      <td>0.1</td>
      <td>2.4</td>
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
      <td>1</td>
      <td>68.0</td>
      <td>69.0</td>
      <td>135.0</td>
      <td>135.0</td>
      <td>69.0</td>
      <td>68.0</td>
      <td>Southpaw</td>
      <td>Orthodox</td>
      <td>1981-10-17</td>
      <td>1988-03-26</td>
      <td>3.21</td>
      <td>5.24</td>
      <td>40.0</td>
      <td>40.0</td>
      <td>2.79</td>
      <td>6.33</td>
      <td>56.0</td>
      <td>57.0</td>
      <td>0.90</td>
      <td>0.16</td>
      <td>30.0</td>
      <td>50.0</td>
      <td>78.0</td>
      <td>76.0</td>
      <td>0.1</td>
      <td>0.2</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>70.0</td>
      <td>71.0</td>
      <td>170.0</td>
      <td>170.0</td>
      <td>72.0</td>
      <td>72.0</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1980-04-10</td>
      <td>1983-03-24</td>
      <td>2.69</td>
      <td>3.29</td>
      <td>43.0</td>
      <td>46.0</td>
      <td>3.13</td>
      <td>3.63</td>
      <td>51.0</td>
      <td>58.0</td>
      <td>2.53</td>
      <td>1.09</td>
      <td>36.0</td>
      <td>34.0</td>
      <td>72.0</td>
      <td>46.0</td>
      <td>0.3</td>
      <td>1.3</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>70.0</td>
      <td>68.0</td>
      <td>155.0</td>
      <td>155.0</td>
      <td>73.0</td>
      <td>71.0</td>
      <td>Orthodox</td>
      <td>Southpaw</td>
      <td>1995-01-03</td>
      <td>1985-08-15</td>
      <td>5.13</td>
      <td>3.65</td>
      <td>52.0</td>
      <td>53.0</td>
      <td>3.70</td>
      <td>1.77</td>
      <td>41.0</td>
      <td>57.0</td>
      <td>0.98</td>
      <td>0.40</td>
      <td>25.0</td>
      <td>25.0</td>
      <td>56.0</td>
      <td>30.0</td>
      <td>1.6</td>
      <td>0.4</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>77.0</td>
      <td>72.0</td>
      <td>265.0</td>
      <td>265.0</td>
      <td>80.0</td>
      <td>72.0</td>
      <td>Orthodox</td>
      <td>Southpaw</td>
      <td>1988-07-28</td>
      <td>1989-12-10</td>
      <td>4.79</td>
      <td>2.31</td>
      <td>50.0</td>
      <td>31.0</td>
      <td>3.31</td>
      <td>4.30</td>
      <td>55.0</td>
      <td>47.0</td>
      <td>0.20</td>
      <td>0.00</td>
      <td>33.0</td>
      <td>0.0</td>
      <td>64.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>74.0</td>
      <td>73.0</td>
      <td>265.0</td>
      <td>265.0</td>
      <td>75.0</td>
      <td>NaN</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1986-08-04</td>
      <td>1982-10-20</td>
      <td>2.08</td>
      <td>1.03</td>
      <td>46.0</td>
      <td>31.0</td>
      <td>1.52</td>
      <td>3.01</td>
      <td>59.0</td>
      <td>55.0</td>
      <td>1.83</td>
      <td>0.00</td>
      <td>41.0</td>
      <td>0.0</td>
      <td>66.0</td>
      <td>57.0</td>
      <td>0.1</td>
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
      <td>80.0</td>
      <td>75.0</td>
      <td>265.0</td>
      <td>260.0</td>
      <td>80.0</td>
      <td>NaN</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1976-03-05</td>
      <td>1978-11-11</td>
      <td>4.23</td>
      <td>2.60</td>
      <td>41.0</td>
      <td>36.0</td>
      <td>2.61</td>
      <td>8.80</td>
      <td>61.0</td>
      <td>40.0</td>
      <td>0.11</td>
      <td>0.00</td>
      <td>100.0</td>
      <td>0.0</td>
      <td>75.0</td>
      <td>90.0</td>
      <td>0.1</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>7515</th>
      <td>1</td>
      <td>75.0</td>
      <td>72.0</td>
      <td>235.0</td>
      <td>265.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Orthodox</td>
      <td>Switch</td>
      <td>1968-01-05</td>
      <td>None</td>
      <td>0.76</td>
      <td>1.35</td>
      <td>83.0</td>
      <td>30.0</td>
      <td>2.12</td>
      <td>3.55</td>
      <td>30.0</td>
      <td>38.0</td>
      <td>4.55</td>
      <td>1.07</td>
      <td>100.0</td>
      <td>33.0</td>
      <td>0.0</td>
      <td>66.0</td>
      <td>2.3</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>7516</th>
      <td>1</td>
      <td>71.0</td>
      <td>70.0</td>
      <td>155.0</td>
      <td>155.0</td>
      <td>76.0</td>
      <td>71.0</td>
      <td>Orthodox</td>
      <td>Southpaw</td>
      <td>1984-02-12</td>
      <td>1983-10-07</td>
      <td>4.94</td>
      <td>1.95</td>
      <td>45.0</td>
      <td>31.0</td>
      <td>4.41</td>
      <td>2.51</td>
      <td>55.0</td>
      <td>63.0</td>
      <td>0.39</td>
      <td>4.08</td>
      <td>35.0</td>
      <td>53.0</td>
      <td>67.0</td>
      <td>92.0</td>
      <td>0.9</td>
      <td>0.8</td>
    </tr>
    <tr>
      <th>7517</th>
      <td>1</td>
      <td>72.0</td>
      <td>74.0</td>
      <td>185.0</td>
      <td>185.0</td>
      <td>74.0</td>
      <td>76.0</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1981-01-28</td>
      <td>1983-08-10</td>
      <td>2.93</td>
      <td>2.65</td>
      <td>50.0</td>
      <td>47.0</td>
      <td>2.90</td>
      <td>2.58</td>
      <td>57.0</td>
      <td>54.0</td>
      <td>1.45</td>
      <td>3.55</td>
      <td>34.0</td>
      <td>54.0</td>
      <td>59.0</td>
      <td>62.0</td>
      <td>0.8</td>
      <td>1.2</td>
    </tr>
    <tr>
      <th>7518</th>
      <td>2</td>
      <td>75.0</td>
      <td>75.0</td>
      <td>235.0</td>
      <td>205.0</td>
      <td>76.0</td>
      <td>79.0</td>
      <td>Orthodox</td>
      <td>Orthodox</td>
      <td>1981-09-02</td>
      <td>1991-06-26</td>
      <td>2.41</td>
      <td>2.78</td>
      <td>44.0</td>
      <td>64.0</td>
      <td>3.02</td>
      <td>0.52</td>
      <td>55.0</td>
      <td>43.0</td>
      <td>1.01</td>
      <td>5.14</td>
      <td>23.0</td>
      <td>55.0</td>
      <td>45.0</td>
      <td>75.0</td>
      <td>0.1</td>
      <td>2.4</td>
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
      <td>1</td>
      <td>68.0</td>
      <td>69.0</td>
      <td>135.0</td>
      <td>135.0</td>
      <td>69.0</td>
      <td>68.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1981-10-17</td>
      <td>1988-03-26</td>
      <td>3.21</td>
      <td>5.24</td>
      <td>40.0</td>
      <td>40.0</td>
      <td>2.79</td>
      <td>6.33</td>
      <td>56.0</td>
      <td>57.0</td>
      <td>0.90</td>
      <td>0.16</td>
      <td>30.0</td>
      <td>50.0</td>
      <td>78.0</td>
      <td>76.0</td>
      <td>0.1</td>
      <td>0.2</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>70.0</td>
      <td>71.0</td>
      <td>170.0</td>
      <td>170.0</td>
      <td>72.0</td>
      <td>72.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>1980-04-10</td>
      <td>1983-03-24</td>
      <td>2.69</td>
      <td>3.29</td>
      <td>43.0</td>
      <td>46.0</td>
      <td>3.13</td>
      <td>3.63</td>
      <td>51.0</td>
      <td>58.0</td>
      <td>2.53</td>
      <td>1.09</td>
      <td>36.0</td>
      <td>34.0</td>
      <td>72.0</td>
      <td>46.0</td>
      <td>0.3</td>
      <td>1.3</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>70.0</td>
      <td>68.0</td>
      <td>155.0</td>
      <td>155.0</td>
      <td>73.0</td>
      <td>71.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1995-01-03</td>
      <td>1985-08-15</td>
      <td>5.13</td>
      <td>3.65</td>
      <td>52.0</td>
      <td>53.0</td>
      <td>3.70</td>
      <td>1.77</td>
      <td>41.0</td>
      <td>57.0</td>
      <td>0.98</td>
      <td>0.40</td>
      <td>25.0</td>
      <td>25.0</td>
      <td>56.0</td>
      <td>30.0</td>
      <td>1.6</td>
      <td>0.4</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>77.0</td>
      <td>72.0</td>
      <td>265.0</td>
      <td>265.0</td>
      <td>80.0</td>
      <td>72.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1988-07-28</td>
      <td>1989-12-10</td>
      <td>4.79</td>
      <td>2.31</td>
      <td>50.0</td>
      <td>31.0</td>
      <td>3.31</td>
      <td>4.30</td>
      <td>55.0</td>
      <td>47.0</td>
      <td>0.20</td>
      <td>0.00</td>
      <td>33.0</td>
      <td>0.0</td>
      <td>64.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>74.0</td>
      <td>73.0</td>
      <td>265.0</td>
      <td>265.0</td>
      <td>75.0</td>
      <td>NaN</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>1986-08-04</td>
      <td>1982-10-20</td>
      <td>2.08</td>
      <td>1.03</td>
      <td>46.0</td>
      <td>31.0</td>
      <td>1.52</td>
      <td>3.01</td>
      <td>59.0</td>
      <td>55.0</td>
      <td>1.83</td>
      <td>0.00</td>
      <td>41.0</td>
      <td>0.0</td>
      <td>66.0</td>
      <td>57.0</td>
      <td>0.1</td>
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
      <td>80.0</td>
      <td>75.0</td>
      <td>265.0</td>
      <td>260.0</td>
      <td>80.0</td>
      <td>NaN</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>1976-03-05</td>
      <td>1978-11-11</td>
      <td>4.23</td>
      <td>2.60</td>
      <td>41.0</td>
      <td>36.0</td>
      <td>2.61</td>
      <td>8.80</td>
      <td>61.0</td>
      <td>40.0</td>
      <td>0.11</td>
      <td>0.00</td>
      <td>100.0</td>
      <td>0.0</td>
      <td>75.0</td>
      <td>90.0</td>
      <td>0.1</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>7515</th>
      <td>1</td>
      <td>75.0</td>
      <td>72.0</td>
      <td>235.0</td>
      <td>265.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1.0</td>
      <td>2.0</td>
      <td>1968-01-05</td>
      <td>None</td>
      <td>0.76</td>
      <td>1.35</td>
      <td>83.0</td>
      <td>30.0</td>
      <td>2.12</td>
      <td>3.55</td>
      <td>30.0</td>
      <td>38.0</td>
      <td>4.55</td>
      <td>1.07</td>
      <td>100.0</td>
      <td>33.0</td>
      <td>0.0</td>
      <td>66.0</td>
      <td>2.3</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>7516</th>
      <td>1</td>
      <td>71.0</td>
      <td>70.0</td>
      <td>155.0</td>
      <td>155.0</td>
      <td>76.0</td>
      <td>71.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1984-02-12</td>
      <td>1983-10-07</td>
      <td>4.94</td>
      <td>1.95</td>
      <td>45.0</td>
      <td>31.0</td>
      <td>4.41</td>
      <td>2.51</td>
      <td>55.0</td>
      <td>63.0</td>
      <td>0.39</td>
      <td>4.08</td>
      <td>35.0</td>
      <td>53.0</td>
      <td>67.0</td>
      <td>92.0</td>
      <td>0.9</td>
      <td>0.8</td>
    </tr>
    <tr>
      <th>7517</th>
      <td>1</td>
      <td>72.0</td>
      <td>74.0</td>
      <td>185.0</td>
      <td>185.0</td>
      <td>74.0</td>
      <td>76.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>1981-01-28</td>
      <td>1983-08-10</td>
      <td>2.93</td>
      <td>2.65</td>
      <td>50.0</td>
      <td>47.0</td>
      <td>2.90</td>
      <td>2.58</td>
      <td>57.0</td>
      <td>54.0</td>
      <td>1.45</td>
      <td>3.55</td>
      <td>34.0</td>
      <td>54.0</td>
      <td>59.0</td>
      <td>62.0</td>
      <td>0.8</td>
      <td>1.2</td>
    </tr>
    <tr>
      <th>7518</th>
      <td>2</td>
      <td>75.0</td>
      <td>75.0</td>
      <td>235.0</td>
      <td>205.0</td>
      <td>76.0</td>
      <td>79.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>1981-09-02</td>
      <td>1991-06-26</td>
      <td>2.41</td>
      <td>2.78</td>
      <td>44.0</td>
      <td>64.0</td>
      <td>3.02</td>
      <td>0.52</td>
      <td>55.0</td>
      <td>43.0</td>
      <td>1.01</td>
      <td>5.14</td>
      <td>23.0</td>
      <td>55.0</td>
      <td>45.0</td>
      <td>75.0</td>
      <td>0.1</td>
      <td>2.4</td>
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


    <Figure size 640x480 with 0 Axes>



    
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
      <td>0.850127</td>
      <td>0.863722</td>
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
      <td>0.485053</td>
      <td>0.488992</td>
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
      <td>1.000000</td>
      <td>1.000000</td>
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
      <td>1.000000</td>
      <td>1.000000</td>
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
      <td>1.000000</td>
      <td>1.000000</td>
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
      <td>1</td>
      <td>68.0</td>
      <td>69.0</td>
      <td>135.0</td>
      <td>135.0</td>
      <td>69.0</td>
      <td>68.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>3.21</td>
      <td>5.24</td>
      <td>40.0</td>
      <td>40.0</td>
      <td>2.79</td>
      <td>6.33</td>
      <td>56.0</td>
      <td>57.0</td>
      <td>0.90</td>
      <td>0.16</td>
      <td>30.0</td>
      <td>50.0</td>
      <td>78.0</td>
      <td>76.0</td>
      <td>0.1</td>
      <td>0.2</td>
      <td>43.0</td>
      <td>36.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>70.0</td>
      <td>71.0</td>
      <td>170.0</td>
      <td>170.0</td>
      <td>72.0</td>
      <td>72.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>2.69</td>
      <td>3.29</td>
      <td>43.0</td>
      <td>46.0</td>
      <td>3.13</td>
      <td>3.63</td>
      <td>51.0</td>
      <td>58.0</td>
      <td>2.53</td>
      <td>1.09</td>
      <td>36.0</td>
      <td>34.0</td>
      <td>72.0</td>
      <td>46.0</td>
      <td>0.3</td>
      <td>1.3</td>
      <td>44.0</td>
      <td>41.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>70.0</td>
      <td>68.0</td>
      <td>155.0</td>
      <td>155.0</td>
      <td>73.0</td>
      <td>71.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>5.13</td>
      <td>3.65</td>
      <td>52.0</td>
      <td>53.0</td>
      <td>3.70</td>
      <td>1.77</td>
      <td>41.0</td>
      <td>57.0</td>
      <td>0.98</td>
      <td>0.40</td>
      <td>25.0</td>
      <td>25.0</td>
      <td>56.0</td>
      <td>30.0</td>
      <td>1.6</td>
      <td>0.4</td>
      <td>29.0</td>
      <td>39.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>77.0</td>
      <td>72.0</td>
      <td>265.0</td>
      <td>265.0</td>
      <td>80.0</td>
      <td>72.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>4.79</td>
      <td>2.31</td>
      <td>50.0</td>
      <td>31.0</td>
      <td>3.31</td>
      <td>4.30</td>
      <td>55.0</td>
      <td>47.0</td>
      <td>0.20</td>
      <td>0.00</td>
      <td>33.0</td>
      <td>0.0</td>
      <td>64.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>36.0</td>
      <td>35.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>74.0</td>
      <td>73.0</td>
      <td>265.0</td>
      <td>265.0</td>
      <td>75.0</td>
      <td>NaN</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>2.08</td>
      <td>1.03</td>
      <td>46.0</td>
      <td>31.0</td>
      <td>1.52</td>
      <td>3.01</td>
      <td>59.0</td>
      <td>55.0</td>
      <td>1.83</td>
      <td>0.00</td>
      <td>41.0</td>
      <td>0.0</td>
      <td>66.0</td>
      <td>57.0</td>
      <td>0.1</td>
      <td>0.0</td>
      <td>38.0</td>
      <td>42.0</td>
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
      <td>80.0</td>
      <td>75.0</td>
      <td>265.0</td>
      <td>260.0</td>
      <td>80.0</td>
      <td>NaN</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>4.23</td>
      <td>2.60</td>
      <td>41.0</td>
      <td>36.0</td>
      <td>2.61</td>
      <td>8.80</td>
      <td>61.0</td>
      <td>40.0</td>
      <td>0.11</td>
      <td>0.00</td>
      <td>100.0</td>
      <td>0.0</td>
      <td>75.0</td>
      <td>90.0</td>
      <td>0.1</td>
      <td>0.0</td>
      <td>48.0</td>
      <td>46.0</td>
    </tr>
    <tr>
      <th>7515</th>
      <td>1</td>
      <td>75.0</td>
      <td>72.0</td>
      <td>235.0</td>
      <td>265.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1.0</td>
      <td>2.0</td>
      <td>0.76</td>
      <td>1.35</td>
      <td>83.0</td>
      <td>30.0</td>
      <td>2.12</td>
      <td>3.55</td>
      <td>30.0</td>
      <td>38.0</td>
      <td>4.55</td>
      <td>1.07</td>
      <td>100.0</td>
      <td>33.0</td>
      <td>0.0</td>
      <td>66.0</td>
      <td>2.3</td>
      <td>0.0</td>
      <td>56.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>7516</th>
      <td>1</td>
      <td>71.0</td>
      <td>70.0</td>
      <td>155.0</td>
      <td>155.0</td>
      <td>76.0</td>
      <td>71.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>4.94</td>
      <td>1.95</td>
      <td>45.0</td>
      <td>31.0</td>
      <td>4.41</td>
      <td>2.51</td>
      <td>55.0</td>
      <td>63.0</td>
      <td>0.39</td>
      <td>4.08</td>
      <td>35.0</td>
      <td>53.0</td>
      <td>67.0</td>
      <td>92.0</td>
      <td>0.9</td>
      <td>0.8</td>
      <td>40.0</td>
      <td>41.0</td>
    </tr>
    <tr>
      <th>7517</th>
      <td>1</td>
      <td>72.0</td>
      <td>74.0</td>
      <td>185.0</td>
      <td>185.0</td>
      <td>74.0</td>
      <td>76.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>2.93</td>
      <td>2.65</td>
      <td>50.0</td>
      <td>47.0</td>
      <td>2.90</td>
      <td>2.58</td>
      <td>57.0</td>
      <td>54.0</td>
      <td>1.45</td>
      <td>3.55</td>
      <td>34.0</td>
      <td>54.0</td>
      <td>59.0</td>
      <td>62.0</td>
      <td>0.8</td>
      <td>1.2</td>
      <td>43.0</td>
      <td>41.0</td>
    </tr>
    <tr>
      <th>7518</th>
      <td>2</td>
      <td>75.0</td>
      <td>75.0</td>
      <td>235.0</td>
      <td>205.0</td>
      <td>76.0</td>
      <td>79.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>2.41</td>
      <td>2.78</td>
      <td>44.0</td>
      <td>64.0</td>
      <td>3.02</td>
      <td>0.52</td>
      <td>55.0</td>
      <td>43.0</td>
      <td>1.01</td>
      <td>5.14</td>
      <td>23.0</td>
      <td>55.0</td>
      <td>45.0</td>
      <td>75.0</td>
      <td>0.1</td>
      <td>2.4</td>
      <td>43.0</td>
      <td>33.0</td>
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
      <td>1.0</td>
      <td>68.0</td>
      <td>69.0</td>
      <td>135.0</td>
      <td>135.0</td>
      <td>69.00000</td>
      <td>68.000000</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>3.21</td>
      <td>5.24</td>
      <td>40.0</td>
      <td>40.0</td>
      <td>2.79</td>
      <td>6.33</td>
      <td>56.0</td>
      <td>57.0</td>
      <td>0.90</td>
      <td>0.16</td>
      <td>30.0</td>
      <td>50.0</td>
      <td>78.0</td>
      <td>76.0</td>
      <td>0.1</td>
      <td>0.2</td>
      <td>43.0</td>
      <td>36.000000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.0</td>
      <td>70.0</td>
      <td>71.0</td>
      <td>170.0</td>
      <td>170.0</td>
      <td>72.00000</td>
      <td>72.000000</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>2.69</td>
      <td>3.29</td>
      <td>43.0</td>
      <td>46.0</td>
      <td>3.13</td>
      <td>3.63</td>
      <td>51.0</td>
      <td>58.0</td>
      <td>2.53</td>
      <td>1.09</td>
      <td>36.0</td>
      <td>34.0</td>
      <td>72.0</td>
      <td>46.0</td>
      <td>0.3</td>
      <td>1.3</td>
      <td>44.0</td>
      <td>41.000000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1.0</td>
      <td>70.0</td>
      <td>68.0</td>
      <td>155.0</td>
      <td>155.0</td>
      <td>73.00000</td>
      <td>71.000000</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>5.13</td>
      <td>3.65</td>
      <td>52.0</td>
      <td>53.0</td>
      <td>3.70</td>
      <td>1.77</td>
      <td>41.0</td>
      <td>57.0</td>
      <td>0.98</td>
      <td>0.40</td>
      <td>25.0</td>
      <td>25.0</td>
      <td>56.0</td>
      <td>30.0</td>
      <td>1.6</td>
      <td>0.4</td>
      <td>29.0</td>
      <td>39.000000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3.0</td>
      <td>77.0</td>
      <td>72.0</td>
      <td>265.0</td>
      <td>265.0</td>
      <td>80.00000</td>
      <td>72.000000</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>4.79</td>
      <td>2.31</td>
      <td>50.0</td>
      <td>31.0</td>
      <td>3.31</td>
      <td>4.30</td>
      <td>55.0</td>
      <td>47.0</td>
      <td>0.20</td>
      <td>0.00</td>
      <td>33.0</td>
      <td>0.0</td>
      <td>64.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>36.0</td>
      <td>35.000000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1.0</td>
      <td>74.0</td>
      <td>73.0</td>
      <td>265.0</td>
      <td>265.0</td>
      <td>75.00000</td>
      <td>71.982162</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>2.08</td>
      <td>1.03</td>
      <td>46.0</td>
      <td>31.0</td>
      <td>1.52</td>
      <td>3.01</td>
      <td>59.0</td>
      <td>55.0</td>
      <td>1.83</td>
      <td>0.00</td>
      <td>41.0</td>
      <td>0.0</td>
      <td>66.0</td>
      <td>57.0</td>
      <td>0.1</td>
      <td>0.0</td>
      <td>38.0</td>
      <td>42.000000</td>
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
      <td>80.0</td>
      <td>75.0</td>
      <td>265.0</td>
      <td>260.0</td>
      <td>80.00000</td>
      <td>71.982162</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>4.23</td>
      <td>2.60</td>
      <td>41.0</td>
      <td>36.0</td>
      <td>2.61</td>
      <td>8.80</td>
      <td>61.0</td>
      <td>40.0</td>
      <td>0.11</td>
      <td>0.00</td>
      <td>100.0</td>
      <td>0.0</td>
      <td>75.0</td>
      <td>90.0</td>
      <td>0.1</td>
      <td>0.0</td>
      <td>48.0</td>
      <td>46.000000</td>
    </tr>
    <tr>
      <th>7515</th>
      <td>1.0</td>
      <td>75.0</td>
      <td>72.0</td>
      <td>235.0</td>
      <td>265.0</td>
      <td>72.13694</td>
      <td>71.982162</td>
      <td>1.0</td>
      <td>2.0</td>
      <td>0.76</td>
      <td>1.35</td>
      <td>83.0</td>
      <td>30.0</td>
      <td>2.12</td>
      <td>3.55</td>
      <td>30.0</td>
      <td>38.0</td>
      <td>4.55</td>
      <td>1.07</td>
      <td>100.0</td>
      <td>33.0</td>
      <td>0.0</td>
      <td>66.0</td>
      <td>2.3</td>
      <td>0.0</td>
      <td>56.0</td>
      <td>38.544971</td>
    </tr>
    <tr>
      <th>7516</th>
      <td>1.0</td>
      <td>71.0</td>
      <td>70.0</td>
      <td>155.0</td>
      <td>155.0</td>
      <td>76.00000</td>
      <td>71.000000</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>4.94</td>
      <td>1.95</td>
      <td>45.0</td>
      <td>31.0</td>
      <td>4.41</td>
      <td>2.51</td>
      <td>55.0</td>
      <td>63.0</td>
      <td>0.39</td>
      <td>4.08</td>
      <td>35.0</td>
      <td>53.0</td>
      <td>67.0</td>
      <td>92.0</td>
      <td>0.9</td>
      <td>0.8</td>
      <td>40.0</td>
      <td>41.000000</td>
    </tr>
    <tr>
      <th>7517</th>
      <td>1.0</td>
      <td>72.0</td>
      <td>74.0</td>
      <td>185.0</td>
      <td>185.0</td>
      <td>74.00000</td>
      <td>76.000000</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>2.93</td>
      <td>2.65</td>
      <td>50.0</td>
      <td>47.0</td>
      <td>2.90</td>
      <td>2.58</td>
      <td>57.0</td>
      <td>54.0</td>
      <td>1.45</td>
      <td>3.55</td>
      <td>34.0</td>
      <td>54.0</td>
      <td>59.0</td>
      <td>62.0</td>
      <td>0.8</td>
      <td>1.2</td>
      <td>43.0</td>
      <td>41.000000</td>
    </tr>
    <tr>
      <th>7518</th>
      <td>2.0</td>
      <td>75.0</td>
      <td>75.0</td>
      <td>235.0</td>
      <td>205.0</td>
      <td>76.00000</td>
      <td>79.000000</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>2.41</td>
      <td>2.78</td>
      <td>44.0</td>
      <td>64.0</td>
      <td>3.02</td>
      <td>0.52</td>
      <td>55.0</td>
      <td>43.0</td>
      <td>1.01</td>
      <td>5.14</td>
      <td>23.0</td>
      <td>55.0</td>
      <td>45.0</td>
      <td>75.0</td>
      <td>0.1</td>
      <td>2.4</td>
      <td>43.0</td>
      <td>33.000000</td>
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

X_ = torch.tensor(X.values, dtype=torch.float32)
y_ = torch.tensor(y, dtype=torch.float32).reshape(-1)

X_train, X_test, y_train, y_test = train_test_split(X_, y_, test_size=0.2, random_state=13)
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
                            lr=0.01)
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
epochs = 2000

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

    Epoch: 0 | Loss: 1.79997, Accuracy: 65.43% | Test loss: 9.36386, Test acc: 33.54%
    Epoch: 10 | Loss: 0.61190, Accuracy: 65.65% | Test loss: 0.59880, Test acc: 66.80%
    Epoch: 20 | Loss: 0.60598, Accuracy: 66.53% | Test loss: 0.59259, Test acc: 67.62%
    Epoch: 30 | Loss: 0.60249, Accuracy: 66.63% | Test loss: 0.58975, Test acc: 68.16%
    Epoch: 40 | Loss: 0.59990, Accuracy: 66.70% | Test loss: 0.58757, Test acc: 68.29%
    Epoch: 50 | Loss: 0.59762, Accuracy: 67.01% | Test loss: 0.58566, Test acc: 68.70%
    Epoch: 60 | Loss: 0.59533, Accuracy: 67.19% | Test loss: 0.58365, Test acc: 68.56%
    Epoch: 70 | Loss: 0.59326, Accuracy: 67.31% | Test loss: 0.58179, Test acc: 68.63%
    Epoch: 80 | Loss: 0.59163, Accuracy: 67.43% | Test loss: 0.58025, Test acc: 68.83%
    Epoch: 90 | Loss: 0.59012, Accuracy: 67.55% | Test loss: 0.57877, Test acc: 68.90%
    Epoch: 100 | Loss: 0.58867, Accuracy: 67.65% | Test loss: 0.57727, Test acc: 69.17%
    Epoch: 110 | Loss: 0.58729, Accuracy: 67.67% | Test loss: 0.57589, Test acc: 69.31%
    Epoch: 120 | Loss: 0.58599, Accuracy: 67.72% | Test loss: 0.57462, Test acc: 69.65%
    Epoch: 130 | Loss: 0.58475, Accuracy: 67.95% | Test loss: 0.57333, Test acc: 69.99%
    Epoch: 140 | Loss: 0.58355, Accuracy: 67.90% | Test loss: 0.57201, Test acc: 69.92%
    Epoch: 150 | Loss: 0.58238, Accuracy: 68.02% | Test loss: 0.57063, Test acc: 69.65%
    Epoch: 160 | Loss: 0.58817, Accuracy: 68.28% | Test loss: 0.57902, Test acc: 67.41%
    Epoch: 170 | Loss: 0.58445, Accuracy: 68.55% | Test loss: 0.57148, Test acc: 68.29%
    Epoch: 180 | Loss: 0.58085, Accuracy: 68.50% | Test loss: 0.56875, Test acc: 69.38%
    Epoch: 190 | Loss: 0.60449, Accuracy: 65.55% | Test loss: 0.58946, Test acc: 67.28%
    Epoch: 200 | Loss: 0.58124, Accuracy: 68.73% | Test loss: 0.56839, Test acc: 68.50%
    Epoch: 210 | Loss: 0.58930, Accuracy: 67.75% | Test loss: 0.57748, Test acc: 67.48%
    Epoch: 220 | Loss: 0.58310, Accuracy: 68.46% | Test loss: 0.56910, Test acc: 68.36%
    Epoch: 230 | Loss: 0.58477, Accuracy: 68.63% | Test loss: 0.57211, Test acc: 67.75%
    Epoch: 240 | Loss: 0.58493, Accuracy: 68.99% | Test loss: 0.57046, Test acc: 68.02%
    Epoch: 250 | Loss: 0.58207, Accuracy: 68.84% | Test loss: 0.56873, Test acc: 68.29%
    Epoch: 260 | Loss: 0.58695, Accuracy: 67.82% | Test loss: 0.57247, Test acc: 67.68%
    Epoch: 270 | Loss: 0.58241, Accuracy: 68.70% | Test loss: 0.56844, Test acc: 68.22%
    Epoch: 280 | Loss: 0.58473, Accuracy: 68.24% | Test loss: 0.57068, Test acc: 67.68%
    Epoch: 290 | Loss: 0.58278, Accuracy: 68.48% | Test loss: 0.56819, Test acc: 68.22%
    Epoch: 300 | Loss: 0.58312, Accuracy: 68.21% | Test loss: 0.56862, Test acc: 68.09%
    Epoch: 310 | Loss: 0.58175, Accuracy: 68.19% | Test loss: 0.56722, Test acc: 68.22%
    Epoch: 320 | Loss: 0.58184, Accuracy: 68.31% | Test loss: 0.56746, Test acc: 68.09%
    Epoch: 330 | Loss: 0.58095, Accuracy: 68.36% | Test loss: 0.56632, Test acc: 68.29%
    Epoch: 340 | Loss: 0.58069, Accuracy: 68.36% | Test loss: 0.56644, Test acc: 68.29%
    Epoch: 350 | Loss: 0.58075, Accuracy: 68.26% | Test loss: 0.56649, Test acc: 68.36%
    Epoch: 360 | Loss: 0.57909, Accuracy: 68.50% | Test loss: 0.56496, Test acc: 68.50%
    Epoch: 370 | Loss: 0.57895, Accuracy: 68.46% | Test loss: 0.56513, Test acc: 68.63%
    Epoch: 380 | Loss: 0.57896, Accuracy: 68.46% | Test loss: 0.56510, Test acc: 68.63%
    Epoch: 390 | Loss: 0.57756, Accuracy: 68.58% | Test loss: 0.56400, Test acc: 68.56%
    Epoch: 400 | Loss: 0.57852, Accuracy: 68.46% | Test loss: 0.56480, Test acc: 68.63%
    Epoch: 410 | Loss: 0.57716, Accuracy: 68.53% | Test loss: 0.56375, Test acc: 68.63%
    Epoch: 420 | Loss: 0.57718, Accuracy: 68.62% | Test loss: 0.56372, Test acc: 68.63%
    Epoch: 430 | Loss: 0.57688, Accuracy: 68.78% | Test loss: 0.56346, Test acc: 68.63%
    Epoch: 440 | Loss: 0.57674, Accuracy: 68.78% | Test loss: 0.56338, Test acc: 68.63%
    Epoch: 450 | Loss: 0.57593, Accuracy: 68.80% | Test loss: 0.56266, Test acc: 68.63%
    Epoch: 460 | Loss: 0.57503, Accuracy: 68.90% | Test loss: 0.56172, Test acc: 68.77%
    Epoch: 470 | Loss: 0.57560, Accuracy: 68.82% | Test loss: 0.56218, Test acc: 68.63%
    Epoch: 480 | Loss: 0.57440, Accuracy: 68.87% | Test loss: 0.56111, Test acc: 68.63%
    Epoch: 490 | Loss: 0.57440, Accuracy: 68.99% | Test loss: 0.56102, Test acc: 68.70%
    Epoch: 500 | Loss: 0.57519, Accuracy: 68.73% | Test loss: 0.56168, Test acc: 68.63%
    Epoch: 510 | Loss: 0.57383, Accuracy: 68.84% | Test loss: 0.56073, Test acc: 68.70%
    Epoch: 520 | Loss: 0.57326, Accuracy: 68.89% | Test loss: 0.55997, Test acc: 68.63%
    Epoch: 530 | Loss: 0.57293, Accuracy: 68.77% | Test loss: 0.55966, Test acc: 68.70%
    Epoch: 540 | Loss: 0.57350, Accuracy: 68.77% | Test loss: 0.55977, Test acc: 68.63%
    Epoch: 550 | Loss: 0.57264, Accuracy: 68.65% | Test loss: 0.55937, Test acc: 68.63%
    Epoch: 560 | Loss: 0.57267, Accuracy: 68.63% | Test loss: 0.55902, Test acc: 68.77%
    Epoch: 570 | Loss: 0.57258, Accuracy: 68.53% | Test loss: 0.55926, Test acc: 68.70%
    Epoch: 580 | Loss: 0.57218, Accuracy: 68.63% | Test loss: 0.55880, Test acc: 68.77%
    Epoch: 590 | Loss: 0.57222, Accuracy: 68.50% | Test loss: 0.55904, Test acc: 68.63%
    Epoch: 600 | Loss: 0.57111, Accuracy: 68.70% | Test loss: 0.55816, Test acc: 68.83%
    Epoch: 610 | Loss: 0.57172, Accuracy: 68.58% | Test loss: 0.55852, Test acc: 68.83%
    Epoch: 620 | Loss: 0.57125, Accuracy: 68.65% | Test loss: 0.55859, Test acc: 68.83%
    Epoch: 630 | Loss: 0.57038, Accuracy: 68.73% | Test loss: 0.55787, Test acc: 68.97%
    Epoch: 640 | Loss: 0.57045, Accuracy: 68.75% | Test loss: 0.55774, Test acc: 68.97%
    Epoch: 650 | Loss: 0.57008, Accuracy: 68.78% | Test loss: 0.55741, Test acc: 69.04%
    Epoch: 660 | Loss: 0.57045, Accuracy: 68.77% | Test loss: 0.55761, Test acc: 68.97%
    Epoch: 670 | Loss: 0.56990, Accuracy: 68.85% | Test loss: 0.55762, Test acc: 69.04%
    Epoch: 680 | Loss: 0.56906, Accuracy: 68.97% | Test loss: 0.55684, Test acc: 69.11%
    Epoch: 690 | Loss: 0.56920, Accuracy: 68.97% | Test loss: 0.55685, Test acc: 69.04%
    Epoch: 700 | Loss: 0.56868, Accuracy: 68.95% | Test loss: 0.55670, Test acc: 69.04%
    Epoch: 710 | Loss: 0.56811, Accuracy: 68.97% | Test loss: 0.55604, Test acc: 69.31%
    Epoch: 720 | Loss: 0.56787, Accuracy: 69.11% | Test loss: 0.55597, Test acc: 69.38%
    Epoch: 730 | Loss: 0.56786, Accuracy: 69.12% | Test loss: 0.55608, Test acc: 69.31%
    Epoch: 740 | Loss: 0.56751, Accuracy: 69.21% | Test loss: 0.55575, Test acc: 69.44%
    Epoch: 750 | Loss: 0.56758, Accuracy: 69.17% | Test loss: 0.55610, Test acc: 69.31%
    Epoch: 760 | Loss: 0.56642, Accuracy: 69.16% | Test loss: 0.55540, Test acc: 69.44%
    Epoch: 770 | Loss: 0.56676, Accuracy: 69.26% | Test loss: 0.55532, Test acc: 69.44%
    Epoch: 780 | Loss: 0.56762, Accuracy: 69.17% | Test loss: 0.55619, Test acc: 69.17%
    Epoch: 790 | Loss: 0.56611, Accuracy: 69.31% | Test loss: 0.55516, Test acc: 69.38%
    Epoch: 800 | Loss: 0.56674, Accuracy: 69.24% | Test loss: 0.55592, Test acc: 69.24%
    Epoch: 810 | Loss: 0.56507, Accuracy: 69.34% | Test loss: 0.55439, Test acc: 69.65%
    Epoch: 820 | Loss: 0.56658, Accuracy: 69.24% | Test loss: 0.55585, Test acc: 69.31%
    Epoch: 830 | Loss: 0.56458, Accuracy: 69.31% | Test loss: 0.55428, Test acc: 69.65%
    Epoch: 840 | Loss: 0.56637, Accuracy: 69.24% | Test loss: 0.55606, Test acc: 69.24%
    Epoch: 850 | Loss: 0.56390, Accuracy: 69.29% | Test loss: 0.55370, Test acc: 69.65%
    Epoch: 860 | Loss: 0.56563, Accuracy: 69.23% | Test loss: 0.55542, Test acc: 69.38%
    Epoch: 870 | Loss: 0.56382, Accuracy: 69.33% | Test loss: 0.55381, Test acc: 69.65%
    Epoch: 880 | Loss: 0.56448, Accuracy: 69.23% | Test loss: 0.55481, Test acc: 69.51%
    Epoch: 890 | Loss: 0.56366, Accuracy: 69.29% | Test loss: 0.55386, Test acc: 69.58%
    Epoch: 900 | Loss: 0.56315, Accuracy: 69.33% | Test loss: 0.55399, Test acc: 69.58%
    Epoch: 910 | Loss: 0.56378, Accuracy: 69.34% | Test loss: 0.55417, Test acc: 69.38%
    Epoch: 920 | Loss: 0.56290, Accuracy: 69.33% | Test loss: 0.55388, Test acc: 69.51%
    Epoch: 930 | Loss: 0.56222, Accuracy: 69.53% | Test loss: 0.55325, Test acc: 69.51%
    Epoch: 940 | Loss: 0.56273, Accuracy: 69.36% | Test loss: 0.55389, Test acc: 69.51%
    Epoch: 950 | Loss: 0.56235, Accuracy: 69.41% | Test loss: 0.55363, Test acc: 69.44%
    Epoch: 960 | Loss: 0.56187, Accuracy: 69.43% | Test loss: 0.55352, Test acc: 69.44%
    Epoch: 970 | Loss: 0.56198, Accuracy: 69.40% | Test loss: 0.55328, Test acc: 69.58%
    Epoch: 980 | Loss: 0.56240, Accuracy: 69.31% | Test loss: 0.55401, Test acc: 69.38%
    Epoch: 990 | Loss: 0.56115, Accuracy: 69.50% | Test loss: 0.55278, Test acc: 69.92%
    Epoch: 1000 | Loss: 0.56150, Accuracy: 69.43% | Test loss: 0.55328, Test acc: 69.65%
    Epoch: 1010 | Loss: 0.56127, Accuracy: 69.40% | Test loss: 0.55318, Test acc: 69.58%
    Epoch: 1020 | Loss: 0.56111, Accuracy: 69.41% | Test loss: 0.55312, Test acc: 69.65%
    Epoch: 1030 | Loss: 0.56082, Accuracy: 69.36% | Test loss: 0.55315, Test acc: 69.72%
    Epoch: 1040 | Loss: 0.56170, Accuracy: 69.28% | Test loss: 0.55361, Test acc: 69.58%
    Epoch: 1050 | Loss: 0.56038, Accuracy: 69.46% | Test loss: 0.55322, Test acc: 69.72%
    Epoch: 1060 | Loss: 0.56106, Accuracy: 69.36% | Test loss: 0.55356, Test acc: 69.65%
    Epoch: 1070 | Loss: 0.56017, Accuracy: 69.36% | Test loss: 0.55323, Test acc: 69.85%
    Epoch: 1080 | Loss: 0.55932, Accuracy: 69.55% | Test loss: 0.55228, Test acc: 69.85%
    Epoch: 1090 | Loss: 0.55977, Accuracy: 69.40% | Test loss: 0.55247, Test acc: 69.85%
    Epoch: 1100 | Loss: 0.55990, Accuracy: 69.33% | Test loss: 0.55311, Test acc: 69.85%
    Epoch: 1110 | Loss: 0.55955, Accuracy: 69.36% | Test loss: 0.55262, Test acc: 69.78%
    Epoch: 1120 | Loss: 0.55937, Accuracy: 69.34% | Test loss: 0.55259, Test acc: 69.85%
    Epoch: 1130 | Loss: 0.55938, Accuracy: 69.38% | Test loss: 0.55258, Test acc: 69.85%
    Epoch: 1140 | Loss: 0.55947, Accuracy: 69.28% | Test loss: 0.55262, Test acc: 69.85%
    Epoch: 1150 | Loss: 0.55900, Accuracy: 69.31% | Test loss: 0.55271, Test acc: 69.85%
    Epoch: 1160 | Loss: 0.55861, Accuracy: 69.36% | Test loss: 0.55258, Test acc: 69.85%
    Epoch: 1170 | Loss: 0.55848, Accuracy: 69.33% | Test loss: 0.55240, Test acc: 69.78%
    Epoch: 1180 | Loss: 0.55804, Accuracy: 69.50% | Test loss: 0.55227, Test acc: 69.78%
    Epoch: 1190 | Loss: 0.55815, Accuracy: 69.53% | Test loss: 0.55219, Test acc: 69.85%
    Epoch: 1200 | Loss: 0.55783, Accuracy: 69.53% | Test loss: 0.55231, Test acc: 69.92%
    Epoch: 1210 | Loss: 0.55812, Accuracy: 69.51% | Test loss: 0.55258, Test acc: 69.85%
    Epoch: 1220 | Loss: 0.55787, Accuracy: 69.48% | Test loss: 0.55249, Test acc: 69.85%
    Epoch: 1230 | Loss: 0.55692, Accuracy: 69.55% | Test loss: 0.55149, Test acc: 70.12%
    Epoch: 1240 | Loss: 0.55751, Accuracy: 69.65% | Test loss: 0.55230, Test acc: 69.85%
    Epoch: 1250 | Loss: 0.55664, Accuracy: 69.65% | Test loss: 0.55175, Test acc: 70.05%
    Epoch: 1260 | Loss: 0.55736, Accuracy: 69.62% | Test loss: 0.55260, Test acc: 69.85%
    Epoch: 1270 | Loss: 0.55562, Accuracy: 69.67% | Test loss: 0.55100, Test acc: 69.99%
    Epoch: 1280 | Loss: 0.55729, Accuracy: 69.60% | Test loss: 0.55278, Test acc: 69.85%
    Epoch: 1290 | Loss: 0.55572, Accuracy: 69.63% | Test loss: 0.55134, Test acc: 69.99%
    Epoch: 1300 | Loss: 0.55629, Accuracy: 69.72% | Test loss: 0.55215, Test acc: 70.05%
    Epoch: 1310 | Loss: 0.55527, Accuracy: 69.56% | Test loss: 0.55085, Test acc: 70.05%
    Epoch: 1320 | Loss: 0.55609, Accuracy: 69.67% | Test loss: 0.55231, Test acc: 69.92%
    Epoch: 1330 | Loss: 0.55513, Accuracy: 69.63% | Test loss: 0.55088, Test acc: 70.12%
    Epoch: 1340 | Loss: 0.55541, Accuracy: 69.70% | Test loss: 0.55154, Test acc: 69.99%
    Epoch: 1350 | Loss: 0.55483, Accuracy: 69.62% | Test loss: 0.55071, Test acc: 70.19%
    Epoch: 1360 | Loss: 0.55533, Accuracy: 69.65% | Test loss: 0.55173, Test acc: 69.92%
    Epoch: 1370 | Loss: 0.55411, Accuracy: 69.63% | Test loss: 0.55007, Test acc: 70.12%
    Epoch: 1380 | Loss: 0.55528, Accuracy: 69.73% | Test loss: 0.55199, Test acc: 69.78%
    Epoch: 1390 | Loss: 0.55357, Accuracy: 69.77% | Test loss: 0.54996, Test acc: 69.99%
    Epoch: 1400 | Loss: 0.55494, Accuracy: 69.78% | Test loss: 0.55183, Test acc: 69.78%
    Epoch: 1410 | Loss: 0.55359, Accuracy: 69.70% | Test loss: 0.54991, Test acc: 69.85%
    Epoch: 1420 | Loss: 0.55487, Accuracy: 69.80% | Test loss: 0.55201, Test acc: 69.85%
    Epoch: 1430 | Loss: 0.55247, Accuracy: 69.72% | Test loss: 0.54928, Test acc: 69.85%
    Epoch: 1440 | Loss: 0.55504, Accuracy: 69.84% | Test loss: 0.55247, Test acc: 69.85%
    Epoch: 1450 | Loss: 0.55210, Accuracy: 69.70% | Test loss: 0.54960, Test acc: 69.78%
    Epoch: 1460 | Loss: 0.55465, Accuracy: 69.84% | Test loss: 0.55181, Test acc: 69.92%
    Epoch: 1470 | Loss: 0.55157, Accuracy: 69.80% | Test loss: 0.54924, Test acc: 69.78%
    Epoch: 1480 | Loss: 0.55440, Accuracy: 69.90% | Test loss: 0.55128, Test acc: 69.78%
    Epoch: 1490 | Loss: 0.55282, Accuracy: 69.70% | Test loss: 0.55064, Test acc: 69.72%
    Epoch: 1500 | Loss: 0.55268, Accuracy: 69.68% | Test loss: 0.55022, Test acc: 69.58%
    Epoch: 1510 | Loss: 0.55281, Accuracy: 69.82% | Test loss: 0.55078, Test acc: 69.58%
    Epoch: 1520 | Loss: 0.55140, Accuracy: 69.80% | Test loss: 0.54954, Test acc: 69.65%
    Epoch: 1530 | Loss: 0.55306, Accuracy: 69.92% | Test loss: 0.55067, Test acc: 69.44%
    Epoch: 1540 | Loss: 0.55137, Accuracy: 69.87% | Test loss: 0.55006, Test acc: 69.65%
    Epoch: 1550 | Loss: 0.55106, Accuracy: 69.89% | Test loss: 0.54935, Test acc: 69.72%
    Epoch: 1560 | Loss: 0.55258, Accuracy: 69.89% | Test loss: 0.55061, Test acc: 69.44%
    Epoch: 1570 | Loss: 0.55069, Accuracy: 69.95% | Test loss: 0.54956, Test acc: 69.65%
    Epoch: 1580 | Loss: 0.55153, Accuracy: 69.84% | Test loss: 0.54994, Test acc: 69.72%
    Epoch: 1590 | Loss: 0.55038, Accuracy: 69.99% | Test loss: 0.54929, Test acc: 69.65%
    Epoch: 1600 | Loss: 0.55110, Accuracy: 69.77% | Test loss: 0.54993, Test acc: 69.65%
    Epoch: 1610 | Loss: 0.55081, Accuracy: 69.80% | Test loss: 0.55013, Test acc: 69.58%
    Epoch: 1620 | Loss: 0.55051, Accuracy: 69.90% | Test loss: 0.54957, Test acc: 69.72%
    Epoch: 1630 | Loss: 0.55044, Accuracy: 69.84% | Test loss: 0.54971, Test acc: 69.65%
    Epoch: 1640 | Loss: 0.55008, Accuracy: 69.89% | Test loss: 0.54955, Test acc: 69.65%
    Epoch: 1650 | Loss: 0.55024, Accuracy: 69.84% | Test loss: 0.54977, Test acc: 69.72%
    Epoch: 1660 | Loss: 0.54941, Accuracy: 69.97% | Test loss: 0.54901, Test acc: 69.78%
    Epoch: 1670 | Loss: 0.55002, Accuracy: 69.90% | Test loss: 0.54966, Test acc: 69.58%
    Epoch: 1680 | Loss: 0.54948, Accuracy: 69.92% | Test loss: 0.54950, Test acc: 69.65%
    Epoch: 1690 | Loss: 0.54929, Accuracy: 69.87% | Test loss: 0.54906, Test acc: 69.72%
    Epoch: 1700 | Loss: 0.54927, Accuracy: 69.95% | Test loss: 0.54952, Test acc: 69.51%
    Epoch: 1710 | Loss: 0.54950, Accuracy: 69.97% | Test loss: 0.54957, Test acc: 69.38%
    Epoch: 1720 | Loss: 0.54816, Accuracy: 70.04% | Test loss: 0.54875, Test acc: 69.65%
    Epoch: 1730 | Loss: 0.54888, Accuracy: 69.92% | Test loss: 0.54905, Test acc: 69.58%
    Epoch: 1740 | Loss: 0.54936, Accuracy: 70.01% | Test loss: 0.54970, Test acc: 69.38%
    Epoch: 1750 | Loss: 0.54774, Accuracy: 70.02% | Test loss: 0.54834, Test acc: 69.78%
    Epoch: 1760 | Loss: 0.54916, Accuracy: 69.95% | Test loss: 0.54947, Test acc: 69.38%
    Epoch: 1770 | Loss: 0.54807, Accuracy: 70.11% | Test loss: 0.54868, Test acc: 69.65%
    Epoch: 1780 | Loss: 0.54910, Accuracy: 69.94% | Test loss: 0.54994, Test acc: 69.31%
    Epoch: 1790 | Loss: 0.54723, Accuracy: 70.04% | Test loss: 0.54810, Test acc: 69.72%
    Epoch: 1800 | Loss: 0.54815, Accuracy: 69.97% | Test loss: 0.54964, Test acc: 69.51%
    Epoch: 1810 | Loss: 0.54721, Accuracy: 70.06% | Test loss: 0.54811, Test acc: 69.72%
    Epoch: 1820 | Loss: 0.54809, Accuracy: 69.94% | Test loss: 0.54915, Test acc: 69.51%
    Epoch: 1830 | Loss: 0.54685, Accuracy: 70.04% | Test loss: 0.54821, Test acc: 69.51%
    Epoch: 1840 | Loss: 0.54738, Accuracy: 70.07% | Test loss: 0.54874, Test acc: 69.51%
    Epoch: 1850 | Loss: 0.54726, Accuracy: 70.01% | Test loss: 0.54861, Test acc: 69.51%
    Epoch: 1860 | Loss: 0.54726, Accuracy: 69.95% | Test loss: 0.54870, Test acc: 69.51%
    Epoch: 1870 | Loss: 0.54614, Accuracy: 70.06% | Test loss: 0.54761, Test acc: 69.65%
    Epoch: 1880 | Loss: 0.54704, Accuracy: 69.99% | Test loss: 0.54899, Test acc: 69.38%
    Epoch: 1890 | Loss: 0.54620, Accuracy: 69.95% | Test loss: 0.54783, Test acc: 69.65%
    Epoch: 1900 | Loss: 0.54645, Accuracy: 69.99% | Test loss: 0.54849, Test acc: 69.38%
    Epoch: 1910 | Loss: 0.54654, Accuracy: 69.99% | Test loss: 0.54837, Test acc: 69.38%
    Epoch: 1920 | Loss: 0.54530, Accuracy: 70.06% | Test loss: 0.54749, Test acc: 69.58%
    Epoch: 1930 | Loss: 0.54709, Accuracy: 69.97% | Test loss: 0.54924, Test acc: 69.04%
    Epoch: 1940 | Loss: 0.54496, Accuracy: 70.14% | Test loss: 0.54743, Test acc: 69.65%
    Epoch: 1950 | Loss: 0.54631, Accuracy: 70.12% | Test loss: 0.54869, Test acc: 69.24%
    Epoch: 1960 | Loss: 0.54534, Accuracy: 69.97% | Test loss: 0.54700, Test acc: 70.05%
    Epoch: 1970 | Loss: 0.54502, Accuracy: 70.02% | Test loss: 0.54781, Test acc: 69.65%
    Epoch: 1980 | Loss: 0.54573, Accuracy: 70.12% | Test loss: 0.54813, Test acc: 69.38%
    Epoch: 1990 | Loss: 0.54486, Accuracy: 70.04% | Test loss: 0.54766, Test acc: 69.51%
    


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

    Fighter 1: 5310, fighter 2: 591
    

Now we have trained our first iteration of our model lets see if we can improve the performance with some feature engineering.

## Feature Engineering

Some of the features we think we can engineer from our data preprocessing steps include the following:
  1. Age from the DOB column
  2. Clustering to determine fighter styles
  3. Balance our target variable
  4. Scale our data
  


## Clustering
Clustering is a type of unsupervised learning that can be used to find patterns in our data. Common examples include customer segmentation. In our problem, I want to see if we can detect different fighter styles based on a fighters stats. At a high level this seems like a reasonable assumption, E.G. fighters who are considered strikers probably don't attempt many takedowns, counter-intuitively they may defend more takedowns. 

The reason I want to do this is to see how fight styles compare against each other for example do strikers do better against other strikers or wrestlers?

One other factor we should consider is weight class, again this comes from domain knowledge (it is very important!), the reason we should consider this is heavyweights are likely to throw less punches/takedowns etc. than smaller fighters due to the amount of energy required to do so! Before we take this for granted though lets visualise our hypothesis!

To do this I will create a new feature for the visualization which calculate the total significant strikes attempted in a fight, this is made by combining fighter_a_sig_stikes_att and the same column for fighter_b then visualise the average by weight class.





```python
# Checking if weight class affects fighter output
strikes_data = dataframe[['fighter_a_SLpM', 'fighter_b_SLpM', 'fighter_a_weight']]
strikes_data['total_stikes'] = (strikes_data['fighter_a_SLpM'] + 
strikes_data['fighter_b_SLpM'])
strikes_data = strikes_data.drop(['fighter_a_SLpM', 'fighter_b_SLpM'], axis=1)

# anything over 205 pounds is a heavy weight so lets group them together
strikes_data.loc[strikes_data["fighter_a_weight"] > 205, "fighter_a_weight"] = 265
strikes_data = strikes_data.groupby('fighter_a_weight').filter(lambda x : len(x)>20)


# Visualise 
ax = sns.barplot(x='fighter_a_weight', y='total_stikes', data=strikes_data)
```

    C:\Users\lanna\Anaconda3\lib\site-packages\ipykernel_launcher.py:4: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      after removing the cwd from sys.path.
    


    
![png](output_42_1.png)
    


From the graph above we can see the difference in significant strikes per minute is around 2 between a heavyweight and strawweight. Over the course of a fight this is roughly 30 extra significant strikes

## Scaling data

Finally, before we cluster we need to scale our data as clustering uses a distance based measure to assign points to clusters the performance can be greatly affected by non-scaled data!


```python
# Scaling our data

from sklearn.preprocessing import StandardScaler

scaler = StandardScaler()

X_scaled = pd.DataFrame(scaler.fit_transform(X), columns = X.columns)
X_scaled
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
      <td>-0.659640</td>
      <td>-0.373257</td>
      <td>-0.931184</td>
      <td>-0.906098</td>
      <td>-0.757287</td>
      <td>-1.013374</td>
      <td>-1.753881</td>
      <td>0.279421</td>
      <td>-0.148090</td>
      <td>1.353419</td>
      <td>-0.445337</td>
      <td>-0.261940</td>
      <td>-0.400209</td>
      <td>1.855950</td>
      <td>0.171667</td>
      <td>0.408279</td>
      <td>-0.549418</td>
      <td>-0.995601</td>
      <td>-0.475411</td>
      <td>0.656339</td>
      <td>0.859495</td>
      <td>0.810857</td>
      <td>-0.722890</td>
      <td>-0.495254</td>
      <td>0.603610</td>
      <td>-0.405001</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-0.093005</td>
      <td>0.198802</td>
      <td>0.036010</td>
      <td>0.047716</td>
      <td>-0.033222</td>
      <td>0.004272</td>
      <td>0.308307</td>
      <td>0.279421</td>
      <td>-0.544252</td>
      <td>0.016093</td>
      <td>-0.120643</td>
      <td>0.280671</td>
      <td>-0.122237</td>
      <td>0.116667</td>
      <td>-0.338397</td>
      <td>0.493946</td>
      <td>0.739132</td>
      <td>-0.282130</td>
      <td>-0.153899</td>
      <td>-0.092313</td>
      <td>0.567108</td>
      <td>-0.441215</td>
      <td>-0.458027</td>
      <td>0.860401</td>
      <td>0.760486</td>
      <td>0.386795</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-0.093005</td>
      <td>-0.659287</td>
      <td>-0.378502</td>
      <td>-0.361061</td>
      <td>0.208133</td>
      <td>-0.250140</td>
      <td>0.308307</td>
      <td>-1.777205</td>
      <td>1.314660</td>
      <td>0.262984</td>
      <td>0.853438</td>
      <td>0.913718</td>
      <td>0.343775</td>
      <td>-1.081505</td>
      <td>-1.358526</td>
      <td>0.408279</td>
      <td>-0.486177</td>
      <td>-0.811480</td>
      <td>-0.743338</td>
      <td>-0.513429</td>
      <td>-0.212588</td>
      <td>-1.108987</td>
      <td>1.263586</td>
      <td>-0.248771</td>
      <td>-1.592649</td>
      <td>0.070077</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1.040266</td>
      <td>0.770862</td>
      <td>2.661251</td>
      <td>2.636643</td>
      <td>0.690843</td>
      <td>-0.000267</td>
      <td>0.308307</td>
      <td>0.279421</td>
      <td>-1.008979</td>
      <td>-1.533832</td>
      <td>0.204050</td>
      <td>-1.075856</td>
      <td>-1.438516</td>
      <td>-0.282724</td>
      <td>0.477706</td>
      <td>0.236945</td>
      <td>0.185767</td>
      <td>-1.118349</td>
      <td>0.114028</td>
      <td>-1.683198</td>
      <td>0.274722</td>
      <td>0.017878</td>
      <td>-0.722890</td>
      <td>-0.741737</td>
      <td>-0.180768</td>
      <td>0.545154</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1.606901</td>
      <td>3.631160</td>
      <td>2.661251</td>
      <td>2.636643</td>
      <td>1.897618</td>
      <td>3.057209</td>
      <td>0.308307</td>
      <td>0.279421</td>
      <td>0.202360</td>
      <td>-0.100494</td>
      <td>2.152213</td>
      <td>0.371107</td>
      <td>-0.686356</td>
      <td>0.380781</td>
      <td>0.273680</td>
      <td>-0.534056</td>
      <td>-0.138347</td>
      <td>-0.688732</td>
      <td>0.864223</td>
      <td>0.469176</td>
      <td>0.615839</td>
      <td>-0.065594</td>
      <td>0.204132</td>
      <td>1.353366</td>
      <td>0.760486</td>
      <td>-0.405001</td>
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
    </tr>
    <tr>
      <th>7372</th>
      <td>2.740172</td>
      <td>1.342922</td>
      <td>2.661251</td>
      <td>2.500383</td>
      <td>1.897618</td>
      <td>-0.000267</td>
      <td>0.308307</td>
      <td>0.279421</td>
      <td>0.628996</td>
      <td>-0.457114</td>
      <td>-0.337106</td>
      <td>-0.623680</td>
      <td>-0.547370</td>
      <td>3.447072</td>
      <td>0.681731</td>
      <td>-1.048057</td>
      <td>-1.173930</td>
      <td>-1.118349</td>
      <td>3.275563</td>
      <td>-1.683198</td>
      <td>0.713301</td>
      <td>1.395157</td>
      <td>-0.722890</td>
      <td>-0.741737</td>
      <td>1.387988</td>
      <td>1.178591</td>
    </tr>
    <tr>
      <th>7373</th>
      <td>1.323584</td>
      <td>0.484832</td>
      <td>1.832228</td>
      <td>2.636643</td>
      <td>-0.000171</td>
      <td>-0.000267</td>
      <td>0.308307</td>
      <td>2.336047</td>
      <td>-2.014620</td>
      <td>-1.314374</td>
      <td>4.208607</td>
      <td>-1.166291</td>
      <td>-0.947977</td>
      <td>0.065133</td>
      <td>-2.480667</td>
      <td>-1.219391</td>
      <td>2.335985</td>
      <td>-0.297473</td>
      <td>3.275563</td>
      <td>-0.139104</td>
      <td>-2.941526</td>
      <td>0.393499</td>
      <td>2.190608</td>
      <td>-0.741737</td>
      <td>2.642994</td>
      <td>-0.001981</td>
    </tr>
    <tr>
      <th>7374</th>
      <td>0.190313</td>
      <td>-0.087228</td>
      <td>-0.378502</td>
      <td>-0.361061</td>
      <td>0.932198</td>
      <td>-0.250140</td>
      <td>0.308307</td>
      <td>-1.777205</td>
      <td>1.169909</td>
      <td>-0.902889</td>
      <td>0.095819</td>
      <td>-1.075856</td>
      <td>0.924246</td>
      <td>-0.604813</td>
      <td>0.069654</td>
      <td>0.922280</td>
      <td>-0.952584</td>
      <td>2.011719</td>
      <td>-0.207484</td>
      <td>0.796711</td>
      <td>0.323453</td>
      <td>1.478628</td>
      <td>0.336564</td>
      <td>0.244194</td>
      <td>0.132983</td>
      <td>0.386795</td>
    </tr>
    <tr>
      <th>7375</th>
      <td>0.473631</td>
      <td>1.056892</td>
      <td>0.450522</td>
      <td>0.456494</td>
      <td>0.449488</td>
      <td>1.021917</td>
      <td>0.308307</td>
      <td>0.279421</td>
      <td>-0.361408</td>
      <td>-0.422824</td>
      <td>0.636976</td>
      <td>0.371107</td>
      <td>-0.310277</td>
      <td>-0.559720</td>
      <td>0.273680</td>
      <td>0.151279</td>
      <td>-0.114632</td>
      <td>1.605117</td>
      <td>-0.261070</td>
      <td>0.843502</td>
      <td>-0.066395</td>
      <td>0.226557</td>
      <td>0.204132</td>
      <td>0.737159</td>
      <td>0.603610</td>
      <td>0.386795</td>
    </tr>
    <tr>
      <th>7376</th>
      <td>1.323584</td>
      <td>1.342922</td>
      <td>1.832228</td>
      <td>1.001531</td>
      <td>0.932198</td>
      <td>1.785151</td>
      <td>0.308307</td>
      <td>0.279421</td>
      <td>-0.757569</td>
      <td>-0.333669</td>
      <td>-0.012412</td>
      <td>1.908505</td>
      <td>-0.212169</td>
      <td>-1.886729</td>
      <td>0.069654</td>
      <td>-0.791056</td>
      <td>-0.462461</td>
      <td>2.824923</td>
      <td>-0.850509</td>
      <td>0.890292</td>
      <td>-0.748629</td>
      <td>0.769121</td>
      <td>-0.722890</td>
      <td>2.216056</td>
      <td>0.603610</td>
      <td>-0.880078</td>
    </tr>
  </tbody>
</table>
<p>7377 rows × 26 columns</p>
</div>




```python
# Lets cluster our fighters - My assumption based on domain knowledge is we have 
# different style of fighters, which might help us predict the winner
from sklearn.cluster import KMeans
from sklearn.preprocessing import scale
from mpl_toolkits.mplot3d import Axes3D

# intialise our clusterer and fit to our data
kmeans = KMeans(n_clusters=3, random_state=13)
kmeans.fit(X_scaled)

```




    KMeans(n_clusters=3, random_state=13)



## How many clusters do we need?
When deciding on how many clusters we need we can use the elbow method to visualize the change the cluster variability. 

To do this we iteratively change the number of clusters and measure the mean distance to the clusters. As the number of clusters increase the mean distance to each cluster should reduce until we have just as many clusters as data points which would leave a mean distance of 0 as each point would be its own cluster. However, at some point on the graph the reduction in mean distance will slow and this will indicate that this number of clusters is suitable for our problem.


```python
# calculate the best number of clusters using the inertia value

# set up a range of clusters to try
cluster_no = range(2, 20)

# create a list to hold the inertia values
inertia = []

for k in cluster_no:
    kmeans = KMeans(n_clusters=k, random_state=13)
    kmeans.fit(X_scaled)
    u = kmeans.inertia_
    inertia.append(u)
    print(f'Clusters {k}: Inertia value: {u}')
```

    Clusters 2: Inertia value: 165406.0933305111
    Clusters 3: Inertia value: 149465.3274636645
    Clusters 4: Inertia value: 140192.64305129534
    Clusters 5: Inertia value: 134459.5603617337
    Clusters 6: Inertia value: 130704.13958993711
    Clusters 7: Inertia value: 127087.97247562552
    Clusters 8: Inertia value: 124357.66812469206
    Clusters 9: Inertia value: 121869.31961744385
    Clusters 10: Inertia value: 119600.13947614147
    Clusters 11: Inertia value: 117545.55702861525
    Clusters 12: Inertia value: 115670.38418609202
    Clusters 13: Inertia value: 114136.01391331373
    Clusters 14: Inertia value: 112906.13747207093
    Clusters 15: Inertia value: 111342.16012277265
    Clusters 16: Inertia value: 109985.98321173515
    Clusters 17: Inertia value: 108849.56092588395
    Clusters 18: Inertia value: 107846.23671376989
    Clusters 19: Inertia value: 106940.43680069777
    


```python
# create the elbow plot
fig, ax1 = plt.subplots(1, figsize =(16, 8))
x = np.arange(len(cluster_no))
ax1.plot(x, inertia)
ax1.set_xticks(x)
ax1.set_xticklabels(cluster_no, rotation='45')
plt.xlabel('Number of Clusters')
plt.ylabel('Inertia Score')
plt.title('Inertia for k value')
```




    Text(0.5, 1.0, 'Inertia for k value')




    
![png](output_49_1.png)
    


## Balancing our dataset



```python
# Import the necessary libraries
from imblearn.over_sampling import SMOTE

# Creating an instance of SMOTE
smote = SMOTE()

# Balancing the data
X_resampled, y_resampled = smote.fit_resample(X_scaled, y)
```

## Second model run through


```python
# instantiate our model
model_1 = ufc_fighter_v0().to(device)
model_1
```




    ufc_fighter_v0(
      (layer_1): Linear(in_features=26, out_features=52, bias=True)
      (layer_2): Linear(in_features=52, out_features=52, bias=True)
      (layer_3): Linear(in_features=52, out_features=1, bias=True)
      (relu): ReLU()
    )




```python
# Set up a loss function
loss_fn_1 = nn.BCEWithLogitsLoss() # BCEWithLogitsLoss = sigmoid built-in

# Create an optimizer
optimizer_1 = torch.optim.SGD(params=model_1.parameters(), 
                            lr=0.01)
```


```python
X_resampled_ = torch.tensor(X_resampled.values, dtype=torch.float32)
y_resampled_ = torch.tensor(y_resampled, dtype=torch.float32).reshape(-1)

X_train, X_test, y_train, y_test = train_test_split(X_resampled_, y_resampled_, test_size=0.2, random_state=13)
```


```python
# Lets build our training loop

# number of epochs we want to run our training loop for
epochs = 2000

# transfer our data onto the target device
X_train, y_train = X_train.to(device), y_train.to(device)
X_test, y_test = X_test.to(device), y_test.to(device)

# set up the training loop
for epoch in range(epochs):
    model_1.train()
    
    # forward pass
    y_logits = model_1(X_train).squeeze() # What is the purpose of squeeze?
    y_pred = torch.round(torch.sigmoid(y_logits))

    
    # calculate the loss/accuracy
    loss = loss_fn_1(y_logits, y_train)
    acc = accuracy_fn(y_true=y_train, y_pred=y_pred)
    
    # optimizer zero grad
    optimizer_1.zero_grad() # Why do we zero grad?
    
    # calculate the loss
    loss.backward()
    
    # optimizer
    optimizer_1.step()
    
    #  
    model_1.eval()
    with torch.inference_mode():
        # 1. Forward pass
        test_logits = model_1(X_test).squeeze() 
        test_pred = torch.round(torch.sigmoid(test_logits))
        # 2. Caculate loss/accuracy
        test_loss = loss_fn_1(test_logits,
                            y_test)
        test_acc = accuracy_fn(y_true=y_test,
                               y_pred=test_pred)

    # Print out what's happening every 10 epochs
    if epoch % 10 == 0:
        print(f"Epoch: {epoch} | Loss: {loss:.5f}, Accuracy: {acc:.2f}% | Test loss: {test_loss:.5f}, Test acc: {test_acc:.2f}%")
    
```

    Epoch: 0 | Loss: 0.69218, Accuracy: 53.83% | Test loss: 0.69380, Test acc: 52.14%
    Epoch: 10 | Loss: 0.69117, Accuracy: 54.47% | Test loss: 0.69273, Test acc: 52.87%
    Epoch: 20 | Loss: 0.69021, Accuracy: 54.92% | Test loss: 0.69170, Test acc: 53.54%
    Epoch: 30 | Loss: 0.68928, Accuracy: 55.52% | Test loss: 0.69071, Test acc: 54.31%
    Epoch: 40 | Loss: 0.68837, Accuracy: 56.06% | Test loss: 0.68976, Test acc: 55.03%
    Epoch: 50 | Loss: 0.68749, Accuracy: 56.67% | Test loss: 0.68883, Test acc: 55.70%
    Epoch: 60 | Loss: 0.68663, Accuracy: 57.25% | Test loss: 0.68793, Test acc: 55.96%
    Epoch: 70 | Loss: 0.68578, Accuracy: 58.05% | Test loss: 0.68705, Test acc: 56.17%
    Epoch: 80 | Loss: 0.68495, Accuracy: 58.44% | Test loss: 0.68620, Test acc: 56.84%
    Epoch: 90 | Loss: 0.68413, Accuracy: 58.81% | Test loss: 0.68536, Test acc: 56.79%
    Epoch: 100 | Loss: 0.68333, Accuracy: 59.46% | Test loss: 0.68453, Test acc: 57.00%
    Epoch: 110 | Loss: 0.68253, Accuracy: 59.77% | Test loss: 0.68372, Test acc: 57.51%
    Epoch: 120 | Loss: 0.68174, Accuracy: 60.24% | Test loss: 0.68292, Test acc: 58.34%
    Epoch: 130 | Loss: 0.68096, Accuracy: 60.54% | Test loss: 0.68213, Test acc: 58.70%
    Epoch: 140 | Loss: 0.68017, Accuracy: 61.16% | Test loss: 0.68135, Test acc: 59.47%
    Epoch: 150 | Loss: 0.67939, Accuracy: 61.89% | Test loss: 0.68057, Test acc: 60.30%
    Epoch: 160 | Loss: 0.67861, Accuracy: 62.37% | Test loss: 0.67979, Test acc: 60.87%
    Epoch: 170 | Loss: 0.67783, Accuracy: 62.42% | Test loss: 0.67902, Test acc: 60.87%
    Epoch: 180 | Loss: 0.67705, Accuracy: 62.59% | Test loss: 0.67825, Test acc: 61.23%
    Epoch: 190 | Loss: 0.67627, Accuracy: 62.64% | Test loss: 0.67748, Test acc: 61.59%
    Epoch: 200 | Loss: 0.67549, Accuracy: 62.70% | Test loss: 0.67671, Test acc: 61.80%
    Epoch: 210 | Loss: 0.67470, Accuracy: 62.71% | Test loss: 0.67594, Test acc: 62.00%
    Epoch: 220 | Loss: 0.67390, Accuracy: 63.12% | Test loss: 0.67517, Test acc: 61.49%
    Epoch: 230 | Loss: 0.67311, Accuracy: 63.34% | Test loss: 0.67440, Test acc: 61.59%
    Epoch: 240 | Loss: 0.67231, Accuracy: 63.55% | Test loss: 0.67362, Test acc: 61.85%
    Epoch: 250 | Loss: 0.67151, Accuracy: 63.86% | Test loss: 0.67285, Test acc: 61.90%
    Epoch: 260 | Loss: 0.67070, Accuracy: 64.08% | Test loss: 0.67207, Test acc: 62.16%
    Epoch: 270 | Loss: 0.66988, Accuracy: 64.19% | Test loss: 0.67128, Test acc: 62.21%
    Epoch: 280 | Loss: 0.66906, Accuracy: 64.35% | Test loss: 0.67050, Test acc: 62.47%
    Epoch: 290 | Loss: 0.66824, Accuracy: 64.36% | Test loss: 0.66971, Test acc: 62.57%
    Epoch: 300 | Loss: 0.66741, Accuracy: 64.50% | Test loss: 0.66892, Test acc: 62.78%
    Epoch: 310 | Loss: 0.66658, Accuracy: 64.62% | Test loss: 0.66813, Test acc: 62.62%
    Epoch: 320 | Loss: 0.66573, Accuracy: 64.63% | Test loss: 0.66733, Test acc: 62.78%
    Epoch: 330 | Loss: 0.66489, Accuracy: 64.67% | Test loss: 0.66653, Test acc: 62.62%
    Epoch: 340 | Loss: 0.66403, Accuracy: 64.73% | Test loss: 0.66572, Test acc: 62.78%
    Epoch: 350 | Loss: 0.66317, Accuracy: 64.75% | Test loss: 0.66491, Test acc: 62.78%
    Epoch: 360 | Loss: 0.66230, Accuracy: 64.76% | Test loss: 0.66410, Test acc: 62.73%
    Epoch: 370 | Loss: 0.66143, Accuracy: 64.93% | Test loss: 0.66328, Test acc: 62.67%
    Epoch: 380 | Loss: 0.66056, Accuracy: 65.07% | Test loss: 0.66246, Test acc: 62.78%
    Epoch: 390 | Loss: 0.65968, Accuracy: 65.15% | Test loss: 0.66164, Test acc: 62.93%
    Epoch: 400 | Loss: 0.65879, Accuracy: 65.23% | Test loss: 0.66081, Test acc: 62.98%
    Epoch: 410 | Loss: 0.65790, Accuracy: 65.26% | Test loss: 0.65998, Test acc: 63.04%
    Epoch: 420 | Loss: 0.65700, Accuracy: 65.38% | Test loss: 0.65915, Test acc: 63.19%
    Epoch: 430 | Loss: 0.65610, Accuracy: 65.39% | Test loss: 0.65831, Test acc: 63.19%
    Epoch: 440 | Loss: 0.65519, Accuracy: 65.43% | Test loss: 0.65747, Test acc: 63.19%
    Epoch: 450 | Loss: 0.65428, Accuracy: 65.41% | Test loss: 0.65663, Test acc: 63.14%
    Epoch: 460 | Loss: 0.65337, Accuracy: 65.38% | Test loss: 0.65579, Test acc: 63.29%
    Epoch: 470 | Loss: 0.65245, Accuracy: 65.50% | Test loss: 0.65494, Test acc: 63.40%
    Epoch: 480 | Loss: 0.65152, Accuracy: 65.61% | Test loss: 0.65409, Test acc: 63.50%
    Epoch: 490 | Loss: 0.65060, Accuracy: 65.78% | Test loss: 0.65323, Test acc: 63.60%
    Epoch: 500 | Loss: 0.64967, Accuracy: 65.84% | Test loss: 0.65238, Test acc: 63.71%
    Epoch: 510 | Loss: 0.64874, Accuracy: 65.84% | Test loss: 0.65152, Test acc: 63.81%
    Epoch: 520 | Loss: 0.64780, Accuracy: 65.90% | Test loss: 0.65067, Test acc: 64.02%
    Epoch: 530 | Loss: 0.64687, Accuracy: 65.96% | Test loss: 0.64981, Test acc: 63.96%
    Epoch: 540 | Loss: 0.64593, Accuracy: 66.00% | Test loss: 0.64895, Test acc: 63.96%
    Epoch: 550 | Loss: 0.64499, Accuracy: 66.03% | Test loss: 0.64810, Test acc: 64.02%
    Epoch: 560 | Loss: 0.64405, Accuracy: 66.01% | Test loss: 0.64724, Test acc: 64.07%
    Epoch: 570 | Loss: 0.64311, Accuracy: 66.03% | Test loss: 0.64638, Test acc: 64.22%
    Epoch: 580 | Loss: 0.64216, Accuracy: 66.00% | Test loss: 0.64552, Test acc: 64.33%
    Epoch: 590 | Loss: 0.64122, Accuracy: 65.92% | Test loss: 0.64467, Test acc: 64.43%
    Epoch: 600 | Loss: 0.64028, Accuracy: 66.00% | Test loss: 0.64381, Test acc: 64.38%
    Epoch: 610 | Loss: 0.63934, Accuracy: 66.06% | Test loss: 0.64296, Test acc: 64.33%
    Epoch: 620 | Loss: 0.63840, Accuracy: 66.12% | Test loss: 0.64211, Test acc: 64.33%
    Epoch: 630 | Loss: 0.63746, Accuracy: 66.21% | Test loss: 0.64126, Test acc: 64.27%
    Epoch: 640 | Loss: 0.63652, Accuracy: 66.17% | Test loss: 0.64041, Test acc: 64.17%
    Epoch: 650 | Loss: 0.63558, Accuracy: 66.19% | Test loss: 0.63956, Test acc: 64.17%
    Epoch: 660 | Loss: 0.63465, Accuracy: 66.22% | Test loss: 0.63872, Test acc: 64.17%
    Epoch: 670 | Loss: 0.63371, Accuracy: 66.26% | Test loss: 0.63789, Test acc: 64.27%
    Epoch: 680 | Loss: 0.63278, Accuracy: 66.30% | Test loss: 0.63705, Test acc: 64.27%
    Epoch: 690 | Loss: 0.63186, Accuracy: 66.28% | Test loss: 0.63622, Test acc: 64.27%
    Epoch: 700 | Loss: 0.63093, Accuracy: 66.31% | Test loss: 0.63539, Test acc: 64.33%
    Epoch: 710 | Loss: 0.63001, Accuracy: 66.21% | Test loss: 0.63457, Test acc: 64.43%
    Epoch: 720 | Loss: 0.62910, Accuracy: 66.18% | Test loss: 0.63375, Test acc: 64.43%
    Epoch: 730 | Loss: 0.62819, Accuracy: 66.15% | Test loss: 0.63294, Test acc: 64.38%
    Epoch: 740 | Loss: 0.62729, Accuracy: 66.17% | Test loss: 0.63213, Test acc: 64.38%
    Epoch: 750 | Loss: 0.62639, Accuracy: 66.24% | Test loss: 0.63133, Test acc: 64.27%
    Epoch: 760 | Loss: 0.62549, Accuracy: 66.28% | Test loss: 0.63053, Test acc: 64.48%
    Epoch: 770 | Loss: 0.62460, Accuracy: 66.41% | Test loss: 0.62974, Test acc: 64.53%
    Epoch: 780 | Loss: 0.62372, Accuracy: 66.39% | Test loss: 0.62896, Test acc: 64.53%
    Epoch: 790 | Loss: 0.62284, Accuracy: 66.36% | Test loss: 0.62819, Test acc: 64.58%
    Epoch: 800 | Loss: 0.62197, Accuracy: 66.35% | Test loss: 0.62742, Test acc: 64.69%
    Epoch: 810 | Loss: 0.62110, Accuracy: 66.34% | Test loss: 0.62666, Test acc: 64.69%
    Epoch: 820 | Loss: 0.62025, Accuracy: 66.40% | Test loss: 0.62590, Test acc: 64.69%
    Epoch: 830 | Loss: 0.61939, Accuracy: 66.41% | Test loss: 0.62515, Test acc: 64.64%
    Epoch: 840 | Loss: 0.61855, Accuracy: 66.44% | Test loss: 0.62441, Test acc: 64.79%
    Epoch: 850 | Loss: 0.61771, Accuracy: 66.44% | Test loss: 0.62368, Test acc: 64.95%
    Epoch: 860 | Loss: 0.61689, Accuracy: 66.44% | Test loss: 0.62296, Test acc: 65.00%
    Epoch: 870 | Loss: 0.61606, Accuracy: 66.41% | Test loss: 0.62224, Test acc: 65.00%
    Epoch: 880 | Loss: 0.61525, Accuracy: 66.44% | Test loss: 0.62153, Test acc: 64.95%
    Epoch: 890 | Loss: 0.61444, Accuracy: 66.39% | Test loss: 0.62083, Test acc: 64.95%
    Epoch: 900 | Loss: 0.61364, Accuracy: 66.43% | Test loss: 0.62013, Test acc: 65.05%
    Epoch: 910 | Loss: 0.61284, Accuracy: 66.40% | Test loss: 0.61945, Test acc: 65.20%
    Epoch: 920 | Loss: 0.61206, Accuracy: 66.44% | Test loss: 0.61877, Test acc: 65.36%
    Epoch: 930 | Loss: 0.61128, Accuracy: 66.50% | Test loss: 0.61810, Test acc: 65.36%
    Epoch: 940 | Loss: 0.61051, Accuracy: 66.46% | Test loss: 0.61743, Test acc: 65.36%
    Epoch: 950 | Loss: 0.60974, Accuracy: 66.53% | Test loss: 0.61678, Test acc: 65.31%
    Epoch: 960 | Loss: 0.60899, Accuracy: 66.65% | Test loss: 0.61613, Test acc: 65.20%
    Epoch: 970 | Loss: 0.60824, Accuracy: 66.70% | Test loss: 0.61549, Test acc: 65.31%
    Epoch: 980 | Loss: 0.60751, Accuracy: 66.72% | Test loss: 0.61486, Test acc: 65.31%
    Epoch: 990 | Loss: 0.60678, Accuracy: 66.72% | Test loss: 0.61424, Test acc: 65.31%
    Epoch: 1000 | Loss: 0.60606, Accuracy: 66.72% | Test loss: 0.61362, Test acc: 65.31%
    Epoch: 1010 | Loss: 0.60535, Accuracy: 66.83% | Test loss: 0.61301, Test acc: 65.36%
    Epoch: 1020 | Loss: 0.60464, Accuracy: 66.86% | Test loss: 0.61241, Test acc: 65.41%
    Epoch: 1030 | Loss: 0.60395, Accuracy: 66.97% | Test loss: 0.61182, Test acc: 65.46%
    Epoch: 1040 | Loss: 0.60326, Accuracy: 66.99% | Test loss: 0.61124, Test acc: 65.51%
    Epoch: 1050 | Loss: 0.60258, Accuracy: 66.97% | Test loss: 0.61066, Test acc: 65.67%
    Epoch: 1060 | Loss: 0.60192, Accuracy: 66.94% | Test loss: 0.61010, Test acc: 65.67%
    Epoch: 1070 | Loss: 0.60126, Accuracy: 66.99% | Test loss: 0.60954, Test acc: 65.67%
    Epoch: 1080 | Loss: 0.60060, Accuracy: 67.06% | Test loss: 0.60899, Test acc: 65.67%
    Epoch: 1090 | Loss: 0.59996, Accuracy: 67.15% | Test loss: 0.60845, Test acc: 65.77%
    Epoch: 1100 | Loss: 0.59932, Accuracy: 67.19% | Test loss: 0.60792, Test acc: 65.82%
    Epoch: 1110 | Loss: 0.59870, Accuracy: 67.25% | Test loss: 0.60739, Test acc: 65.72%
    Epoch: 1120 | Loss: 0.59808, Accuracy: 67.29% | Test loss: 0.60687, Test acc: 65.67%
    Epoch: 1130 | Loss: 0.59747, Accuracy: 67.34% | Test loss: 0.60636, Test acc: 65.62%
    Epoch: 1140 | Loss: 0.59686, Accuracy: 67.42% | Test loss: 0.60586, Test acc: 65.67%
    Epoch: 1150 | Loss: 0.59627, Accuracy: 67.47% | Test loss: 0.60536, Test acc: 65.93%
    Epoch: 1160 | Loss: 0.59568, Accuracy: 67.52% | Test loss: 0.60487, Test acc: 65.93%
    Epoch: 1170 | Loss: 0.59510, Accuracy: 67.61% | Test loss: 0.60439, Test acc: 65.88%
    Epoch: 1180 | Loss: 0.59452, Accuracy: 67.64% | Test loss: 0.60392, Test acc: 65.88%
    Epoch: 1190 | Loss: 0.59396, Accuracy: 67.63% | Test loss: 0.60345, Test acc: 65.93%
    Epoch: 1200 | Loss: 0.59340, Accuracy: 67.66% | Test loss: 0.60299, Test acc: 65.98%
    Epoch: 1210 | Loss: 0.59285, Accuracy: 67.70% | Test loss: 0.60253, Test acc: 65.98%
    Epoch: 1220 | Loss: 0.59230, Accuracy: 67.77% | Test loss: 0.60208, Test acc: 65.93%
    Epoch: 1230 | Loss: 0.59177, Accuracy: 67.82% | Test loss: 0.60164, Test acc: 65.88%
    Epoch: 1240 | Loss: 0.59124, Accuracy: 67.78% | Test loss: 0.60121, Test acc: 65.93%
    Epoch: 1250 | Loss: 0.59072, Accuracy: 67.73% | Test loss: 0.60079, Test acc: 66.03%
    Epoch: 1260 | Loss: 0.59020, Accuracy: 67.79% | Test loss: 0.60037, Test acc: 66.08%
    Epoch: 1270 | Loss: 0.58970, Accuracy: 67.83% | Test loss: 0.59996, Test acc: 66.24%
    Epoch: 1280 | Loss: 0.58920, Accuracy: 67.90% | Test loss: 0.59955, Test acc: 66.34%
    Epoch: 1290 | Loss: 0.58871, Accuracy: 67.87% | Test loss: 0.59915, Test acc: 66.39%
    Epoch: 1300 | Loss: 0.58822, Accuracy: 68.01% | Test loss: 0.59876, Test acc: 66.34%
    Epoch: 1310 | Loss: 0.58774, Accuracy: 68.04% | Test loss: 0.59837, Test acc: 66.39%
    Epoch: 1320 | Loss: 0.58727, Accuracy: 68.00% | Test loss: 0.59799, Test acc: 66.60%
    Epoch: 1330 | Loss: 0.58680, Accuracy: 68.03% | Test loss: 0.59761, Test acc: 66.70%
    Epoch: 1340 | Loss: 0.58634, Accuracy: 68.03% | Test loss: 0.59724, Test acc: 66.70%
    Epoch: 1350 | Loss: 0.58589, Accuracy: 68.07% | Test loss: 0.59687, Test acc: 66.70%
    Epoch: 1360 | Loss: 0.58544, Accuracy: 68.12% | Test loss: 0.59651, Test acc: 66.91%
    Epoch: 1370 | Loss: 0.58500, Accuracy: 68.10% | Test loss: 0.59616, Test acc: 67.11%
    Epoch: 1380 | Loss: 0.58456, Accuracy: 68.16% | Test loss: 0.59581, Test acc: 67.06%
    Epoch: 1390 | Loss: 0.58413, Accuracy: 68.18% | Test loss: 0.59546, Test acc: 67.11%
    Epoch: 1400 | Loss: 0.58371, Accuracy: 68.22% | Test loss: 0.59512, Test acc: 67.11%
    Epoch: 1410 | Loss: 0.58329, Accuracy: 68.25% | Test loss: 0.59479, Test acc: 67.11%
    Epoch: 1420 | Loss: 0.58287, Accuracy: 68.26% | Test loss: 0.59446, Test acc: 67.06%
    Epoch: 1430 | Loss: 0.58246, Accuracy: 68.26% | Test loss: 0.59414, Test acc: 67.06%
    Epoch: 1440 | Loss: 0.58206, Accuracy: 68.27% | Test loss: 0.59382, Test acc: 67.06%
    Epoch: 1450 | Loss: 0.58166, Accuracy: 68.28% | Test loss: 0.59351, Test acc: 67.22%
    Epoch: 1460 | Loss: 0.58127, Accuracy: 68.31% | Test loss: 0.59320, Test acc: 67.27%
    Epoch: 1470 | Loss: 0.58088, Accuracy: 68.31% | Test loss: 0.59289, Test acc: 67.37%
    Epoch: 1480 | Loss: 0.58050, Accuracy: 68.32% | Test loss: 0.59259, Test acc: 67.42%
    Epoch: 1490 | Loss: 0.58012, Accuracy: 68.30% | Test loss: 0.59230, Test acc: 67.32%
    Epoch: 1500 | Loss: 0.57974, Accuracy: 68.36% | Test loss: 0.59201, Test acc: 67.37%
    Epoch: 1510 | Loss: 0.57937, Accuracy: 68.36% | Test loss: 0.59172, Test acc: 67.37%
    Epoch: 1520 | Loss: 0.57901, Accuracy: 68.37% | Test loss: 0.59143, Test acc: 67.37%
    Epoch: 1530 | Loss: 0.57865, Accuracy: 68.37% | Test loss: 0.59116, Test acc: 67.48%
    Epoch: 1540 | Loss: 0.57829, Accuracy: 68.49% | Test loss: 0.59088, Test acc: 67.48%
    Epoch: 1550 | Loss: 0.57794, Accuracy: 68.48% | Test loss: 0.59061, Test acc: 67.37%
    Epoch: 1560 | Loss: 0.57759, Accuracy: 68.52% | Test loss: 0.59034, Test acc: 67.42%
    Epoch: 1570 | Loss: 0.57724, Accuracy: 68.49% | Test loss: 0.59008, Test acc: 67.37%
    Epoch: 1580 | Loss: 0.57690, Accuracy: 68.54% | Test loss: 0.58982, Test acc: 67.37%
    Epoch: 1590 | Loss: 0.57656, Accuracy: 68.52% | Test loss: 0.58957, Test acc: 67.42%
    Epoch: 1600 | Loss: 0.57622, Accuracy: 68.48% | Test loss: 0.58931, Test acc: 67.27%
    Epoch: 1610 | Loss: 0.57589, Accuracy: 68.47% | Test loss: 0.58907, Test acc: 67.27%
    Epoch: 1620 | Loss: 0.57557, Accuracy: 68.50% | Test loss: 0.58882, Test acc: 67.48%
    Epoch: 1630 | Loss: 0.57524, Accuracy: 68.56% | Test loss: 0.58858, Test acc: 67.48%
    Epoch: 1640 | Loss: 0.57492, Accuracy: 68.59% | Test loss: 0.58834, Test acc: 67.42%
    Epoch: 1650 | Loss: 0.57461, Accuracy: 68.61% | Test loss: 0.58811, Test acc: 67.37%
    Epoch: 1660 | Loss: 0.57430, Accuracy: 68.62% | Test loss: 0.58787, Test acc: 67.37%
    Epoch: 1670 | Loss: 0.57399, Accuracy: 68.65% | Test loss: 0.58764, Test acc: 67.42%
    Epoch: 1680 | Loss: 0.57368, Accuracy: 68.67% | Test loss: 0.58742, Test acc: 67.27%
    Epoch: 1690 | Loss: 0.57338, Accuracy: 68.70% | Test loss: 0.58720, Test acc: 67.32%
    Epoch: 1700 | Loss: 0.57308, Accuracy: 68.68% | Test loss: 0.58698, Test acc: 67.37%
    Epoch: 1710 | Loss: 0.57279, Accuracy: 68.70% | Test loss: 0.58676, Test acc: 67.48%
    Epoch: 1720 | Loss: 0.57250, Accuracy: 68.72% | Test loss: 0.58655, Test acc: 67.48%
    Epoch: 1730 | Loss: 0.57221, Accuracy: 68.71% | Test loss: 0.58634, Test acc: 67.42%
    Epoch: 1740 | Loss: 0.57193, Accuracy: 68.74% | Test loss: 0.58613, Test acc: 67.48%
    Epoch: 1750 | Loss: 0.57164, Accuracy: 68.79% | Test loss: 0.58593, Test acc: 67.48%
    Epoch: 1760 | Loss: 0.57137, Accuracy: 68.80% | Test loss: 0.58573, Test acc: 67.53%
    Epoch: 1770 | Loss: 0.57109, Accuracy: 68.85% | Test loss: 0.58554, Test acc: 67.63%
    Epoch: 1780 | Loss: 0.57082, Accuracy: 68.90% | Test loss: 0.58535, Test acc: 67.89%
    Epoch: 1790 | Loss: 0.57055, Accuracy: 68.89% | Test loss: 0.58515, Test acc: 67.99%
    Epoch: 1800 | Loss: 0.57028, Accuracy: 68.89% | Test loss: 0.58497, Test acc: 67.99%
    Epoch: 1810 | Loss: 0.57002, Accuracy: 68.94% | Test loss: 0.58478, Test acc: 68.30%
    Epoch: 1820 | Loss: 0.56976, Accuracy: 68.89% | Test loss: 0.58460, Test acc: 68.35%
    Epoch: 1830 | Loss: 0.56950, Accuracy: 68.90% | Test loss: 0.58441, Test acc: 68.25%
    Epoch: 1840 | Loss: 0.56925, Accuracy: 68.96% | Test loss: 0.58423, Test acc: 68.25%
    Epoch: 1850 | Loss: 0.56899, Accuracy: 68.97% | Test loss: 0.58406, Test acc: 68.30%
    Epoch: 1860 | Loss: 0.56875, Accuracy: 68.97% | Test loss: 0.58388, Test acc: 68.35%
    Epoch: 1870 | Loss: 0.56850, Accuracy: 68.99% | Test loss: 0.58371, Test acc: 68.35%
    Epoch: 1880 | Loss: 0.56826, Accuracy: 69.02% | Test loss: 0.58354, Test acc: 68.35%
    Epoch: 1890 | Loss: 0.56801, Accuracy: 69.03% | Test loss: 0.58338, Test acc: 68.35%
    Epoch: 1900 | Loss: 0.56778, Accuracy: 69.03% | Test loss: 0.58321, Test acc: 68.35%
    Epoch: 1910 | Loss: 0.56754, Accuracy: 69.01% | Test loss: 0.58305, Test acc: 68.35%
    Epoch: 1920 | Loss: 0.56731, Accuracy: 69.08% | Test loss: 0.58289, Test acc: 68.35%
    Epoch: 1930 | Loss: 0.56707, Accuracy: 69.06% | Test loss: 0.58273, Test acc: 68.30%
    Epoch: 1940 | Loss: 0.56684, Accuracy: 69.06% | Test loss: 0.58258, Test acc: 68.35%
    Epoch: 1950 | Loss: 0.56662, Accuracy: 69.06% | Test loss: 0.58242, Test acc: 68.35%
    Epoch: 1960 | Loss: 0.56639, Accuracy: 69.11% | Test loss: 0.58227, Test acc: 68.30%
    Epoch: 1970 | Loss: 0.56617, Accuracy: 69.12% | Test loss: 0.58211, Test acc: 68.30%
    Epoch: 1980 | Loss: 0.56595, Accuracy: 69.15% | Test loss: 0.58196, Test acc: 68.40%
    Epoch: 1990 | Loss: 0.56573, Accuracy: 69.15% | Test loss: 0.58181, Test acc: 68.40%
    


```python
# Now that the dataset is balanced lets look at the split of prediction of our model to show its effect!
fighter_1 = 0
fighter_2 = 0
for pred in y_pred:
    if pred == 1:
        fighter_1 += 1
    else:
        fighter_2 += 1
print(f'Fighter 1: {fighter_1}, fighter 2: {fighter_2}')
```

    Fighter 1: 3437, fighter 2: 4310
    

## Model 3


```python
# build a class for our model

class ufc_fighter_v1(nn.Module):
    def __init__(self):
        super().__init__()
        
        # create three layer neural network
        self.layer_1 = nn.Linear(in_features=26, out_features=152)
        self.layer_2 = nn.Linear(in_features=152, out_features=254)
        self.layer_3 = nn.Linear(in_features=254, out_features=1)
        self.relu = nn.ReLU()

    
    def forward(self, x):
        return self.layer_3(self.relu(self.layer_2(self.relu(self.layer_1(x)))))
```


```python
# instantiate our model
model_2 = ufc_fighter_v1().to(device)
model_2
```




    ufc_fighter_v1(
      (layer_1): Linear(in_features=26, out_features=152, bias=True)
      (layer_2): Linear(in_features=152, out_features=254, bias=True)
      (layer_3): Linear(in_features=254, out_features=1, bias=True)
      (relu): ReLU()
    )




```python
# Set up a loss function
loss_fn_2 = nn.BCEWithLogitsLoss() # BCEWithLogitsLoss = sigmoid built-in

# Create an optimizer
optimizer_2 = torch.optim.SGD(params=model_2.parameters(), 
                            lr=0.01)
```


```python
X_resampled_ = torch.tensor(X_resampled.values, dtype=torch.float32)
y_resampled_ = torch.tensor(y_resampled, dtype=torch.float32).reshape(-1)

X_train, X_test, y_train, y_test = train_test_split(X_resampled_, y_resampled_, test_size=0.2, random_state=13)
```


```python
# Lets build our training loop

# number of epochs we want to run our training loop for
epochs = 2000

# transfer our data onto the target device
X_train, y_train = X_train.to(device), y_train.to(device)
X_test, y_test = X_test.to(device), y_test.to(device)

# set up the training loop
for epoch in range(epochs):
    model_2.train()
    
    # forward pass
    y_logits = model_2(X_train).squeeze() # What is the purpose of squeeze?
    y_pred = torch.round(torch.sigmoid(y_logits))

    
    # calculate the loss/accuracy
    loss = loss_fn_2(y_logits, y_train)
    acc = accuracy_fn(y_true=y_train, y_pred=y_pred)
    
    # optimizer zero grad
    optimizer_2.zero_grad() # Why do we zero grad?
    
    # calculate the loss
    loss.backward()
    
    # optimizer
    optimizer_2.step()
    
    #  
    model_2.eval()
    with torch.inference_mode():
        # 1. Forward pass
        test_logits = model_2(X_test).squeeze() 
        test_pred = torch.round(torch.sigmoid(test_logits))
        # 2. Caculate loss/accuracy
        test_loss = loss_fn_2(test_logits,
                            y_test)
        test_acc = accuracy_fn(y_true=y_test,
                               y_pred=test_pred)

    # Print out what's happening every 10 epochs
    if epoch % 10 == 0:
        print(f"Epoch: {epoch} | Loss: {loss:.5f}, Accuracy: {acc:.2f}% | Test loss: {test_loss:.5f}, Test acc: {test_acc:.2f}%")
    
```

    Epoch: 0 | Loss: 0.69376, Accuracy: 51.54% | Test loss: 0.69366, Test acc: 51.94%
    Epoch: 10 | Loss: 0.69138, Accuracy: 53.97% | Test loss: 0.69126, Test acc: 54.10%
    Epoch: 20 | Loss: 0.68914, Accuracy: 55.85% | Test loss: 0.68901, Test acc: 56.32%
    Epoch: 30 | Loss: 0.68701, Accuracy: 58.04% | Test loss: 0.68688, Test acc: 59.06%
    Epoch: 40 | Loss: 0.68496, Accuracy: 59.77% | Test loss: 0.68485, Test acc: 60.61%
    Epoch: 50 | Loss: 0.68300, Accuracy: 60.90% | Test loss: 0.68291, Test acc: 61.69%
    Epoch: 60 | Loss: 0.68109, Accuracy: 61.61% | Test loss: 0.68105, Test acc: 62.21%
    Epoch: 70 | Loss: 0.67925, Accuracy: 62.66% | Test loss: 0.67925, Test acc: 63.24%
    Epoch: 80 | Loss: 0.67745, Accuracy: 63.20% | Test loss: 0.67751, Test acc: 63.24%
    Epoch: 90 | Loss: 0.67569, Accuracy: 63.59% | Test loss: 0.67581, Test acc: 63.81%
    Epoch: 100 | Loss: 0.67397, Accuracy: 63.93% | Test loss: 0.67416, Test acc: 63.76%
    Epoch: 110 | Loss: 0.67228, Accuracy: 64.23% | Test loss: 0.67254, Test acc: 64.07%
    Epoch: 120 | Loss: 0.67062, Accuracy: 64.19% | Test loss: 0.67096, Test acc: 64.38%
    Epoch: 130 | Loss: 0.66899, Accuracy: 64.22% | Test loss: 0.66941, Test acc: 64.02%
    Epoch: 140 | Loss: 0.66739, Accuracy: 64.22% | Test loss: 0.66788, Test acc: 63.91%
    Epoch: 150 | Loss: 0.66581, Accuracy: 64.37% | Test loss: 0.66639, Test acc: 64.07%
    Epoch: 160 | Loss: 0.66425, Accuracy: 64.46% | Test loss: 0.66491, Test acc: 64.02%
    Epoch: 170 | Loss: 0.66271, Accuracy: 64.35% | Test loss: 0.66346, Test acc: 63.96%
    Epoch: 180 | Loss: 0.66118, Accuracy: 64.35% | Test loss: 0.66203, Test acc: 64.12%
    Epoch: 190 | Loss: 0.65968, Accuracy: 64.48% | Test loss: 0.66062, Test acc: 63.71%
    Epoch: 200 | Loss: 0.65819, Accuracy: 64.57% | Test loss: 0.65922, Test acc: 63.71%
    Epoch: 210 | Loss: 0.65672, Accuracy: 64.66% | Test loss: 0.65785, Test acc: 63.71%
    Epoch: 220 | Loss: 0.65527, Accuracy: 64.62% | Test loss: 0.65649, Test acc: 63.66%
    Epoch: 230 | Loss: 0.65383, Accuracy: 64.62% | Test loss: 0.65515, Test acc: 63.91%
    Epoch: 240 | Loss: 0.65241, Accuracy: 64.72% | Test loss: 0.65382, Test acc: 64.02%
    Epoch: 250 | Loss: 0.65100, Accuracy: 64.72% | Test loss: 0.65251, Test acc: 64.02%
    Epoch: 260 | Loss: 0.64960, Accuracy: 64.81% | Test loss: 0.65122, Test acc: 64.12%
    Epoch: 270 | Loss: 0.64822, Accuracy: 64.76% | Test loss: 0.64994, Test acc: 64.27%
    Epoch: 280 | Loss: 0.64685, Accuracy: 64.84% | Test loss: 0.64867, Test acc: 64.17%
    Epoch: 290 | Loss: 0.64550, Accuracy: 64.89% | Test loss: 0.64742, Test acc: 64.22%
    Epoch: 300 | Loss: 0.64416, Accuracy: 64.98% | Test loss: 0.64618, Test acc: 64.17%
    Epoch: 310 | Loss: 0.64284, Accuracy: 65.01% | Test loss: 0.64496, Test acc: 64.27%
    Epoch: 320 | Loss: 0.64153, Accuracy: 65.03% | Test loss: 0.64375, Test acc: 64.38%
    Epoch: 330 | Loss: 0.64023, Accuracy: 64.94% | Test loss: 0.64256, Test acc: 64.43%
    Epoch: 340 | Loss: 0.63895, Accuracy: 65.02% | Test loss: 0.64138, Test acc: 64.48%
    Epoch: 350 | Loss: 0.63767, Accuracy: 65.12% | Test loss: 0.64022, Test acc: 64.53%
    Epoch: 360 | Loss: 0.63642, Accuracy: 65.10% | Test loss: 0.63906, Test acc: 64.64%
    Epoch: 370 | Loss: 0.63518, Accuracy: 65.15% | Test loss: 0.63793, Test acc: 64.69%
    Epoch: 380 | Loss: 0.63395, Accuracy: 65.17% | Test loss: 0.63680, Test acc: 64.69%
    Epoch: 390 | Loss: 0.63273, Accuracy: 65.24% | Test loss: 0.63569, Test acc: 64.79%
    Epoch: 400 | Loss: 0.63153, Accuracy: 65.25% | Test loss: 0.63459, Test acc: 64.74%
    Epoch: 410 | Loss: 0.63034, Accuracy: 65.23% | Test loss: 0.63351, Test acc: 64.74%
    Epoch: 420 | Loss: 0.62916, Accuracy: 65.34% | Test loss: 0.63244, Test acc: 64.84%
    Epoch: 430 | Loss: 0.62799, Accuracy: 65.38% | Test loss: 0.63138, Test acc: 64.84%
    Epoch: 440 | Loss: 0.62684, Accuracy: 65.42% | Test loss: 0.63033, Test acc: 65.05%
    Epoch: 450 | Loss: 0.62570, Accuracy: 65.39% | Test loss: 0.62929, Test acc: 64.89%
    Epoch: 460 | Loss: 0.62457, Accuracy: 65.42% | Test loss: 0.62827, Test acc: 64.89%
    Epoch: 470 | Loss: 0.62346, Accuracy: 65.50% | Test loss: 0.62726, Test acc: 64.89%
    Epoch: 480 | Loss: 0.62236, Accuracy: 65.51% | Test loss: 0.62627, Test acc: 65.05%
    Epoch: 490 | Loss: 0.62127, Accuracy: 65.44% | Test loss: 0.62528, Test acc: 65.10%
    Epoch: 500 | Loss: 0.62020, Accuracy: 65.51% | Test loss: 0.62431, Test acc: 65.10%
    Epoch: 510 | Loss: 0.61913, Accuracy: 65.50% | Test loss: 0.62335, Test acc: 65.20%
    Epoch: 520 | Loss: 0.61808, Accuracy: 65.52% | Test loss: 0.62241, Test acc: 65.15%
    Epoch: 530 | Loss: 0.61705, Accuracy: 65.57% | Test loss: 0.62147, Test acc: 65.10%
    Epoch: 540 | Loss: 0.61602, Accuracy: 65.64% | Test loss: 0.62055, Test acc: 65.15%
    Epoch: 550 | Loss: 0.61501, Accuracy: 65.69% | Test loss: 0.61964, Test acc: 65.36%
    Epoch: 560 | Loss: 0.61401, Accuracy: 65.69% | Test loss: 0.61874, Test acc: 65.36%
    Epoch: 570 | Loss: 0.61302, Accuracy: 65.77% | Test loss: 0.61786, Test acc: 65.36%
    Epoch: 580 | Loss: 0.61204, Accuracy: 65.87% | Test loss: 0.61698, Test acc: 65.26%
    Epoch: 590 | Loss: 0.61107, Accuracy: 65.94% | Test loss: 0.61612, Test acc: 65.31%
    Epoch: 600 | Loss: 0.61012, Accuracy: 65.99% | Test loss: 0.61527, Test acc: 65.36%
    Epoch: 610 | Loss: 0.60918, Accuracy: 66.05% | Test loss: 0.61443, Test acc: 65.46%
    Epoch: 620 | Loss: 0.60824, Accuracy: 66.13% | Test loss: 0.61360, Test acc: 65.41%
    Epoch: 630 | Loss: 0.60733, Accuracy: 66.14% | Test loss: 0.61278, Test acc: 65.31%
    Epoch: 640 | Loss: 0.60642, Accuracy: 66.28% | Test loss: 0.61198, Test acc: 65.36%
    Epoch: 650 | Loss: 0.60552, Accuracy: 66.39% | Test loss: 0.61118, Test acc: 65.36%
    Epoch: 660 | Loss: 0.60464, Accuracy: 66.46% | Test loss: 0.61040, Test acc: 65.57%
    Epoch: 670 | Loss: 0.60376, Accuracy: 66.50% | Test loss: 0.60962, Test acc: 65.51%
    Epoch: 680 | Loss: 0.60290, Accuracy: 66.70% | Test loss: 0.60886, Test acc: 65.57%
    Epoch: 690 | Loss: 0.60205, Accuracy: 66.70% | Test loss: 0.60811, Test acc: 65.51%
    Epoch: 700 | Loss: 0.60121, Accuracy: 66.79% | Test loss: 0.60737, Test acc: 65.67%
    Epoch: 710 | Loss: 0.60039, Accuracy: 66.84% | Test loss: 0.60664, Test acc: 65.77%
    Epoch: 720 | Loss: 0.59957, Accuracy: 66.93% | Test loss: 0.60593, Test acc: 65.72%
    Epoch: 730 | Loss: 0.59877, Accuracy: 66.86% | Test loss: 0.60522, Test acc: 65.62%
    Epoch: 740 | Loss: 0.59798, Accuracy: 66.88% | Test loss: 0.60453, Test acc: 65.67%
    Epoch: 750 | Loss: 0.59719, Accuracy: 66.80% | Test loss: 0.60385, Test acc: 65.82%
    Epoch: 760 | Loss: 0.59642, Accuracy: 66.79% | Test loss: 0.60317, Test acc: 65.88%
    Epoch: 770 | Loss: 0.59566, Accuracy: 66.89% | Test loss: 0.60251, Test acc: 66.03%
    Epoch: 780 | Loss: 0.59491, Accuracy: 67.02% | Test loss: 0.60186, Test acc: 66.03%
    Epoch: 790 | Loss: 0.59417, Accuracy: 67.08% | Test loss: 0.60121, Test acc: 66.13%
    Epoch: 800 | Loss: 0.59344, Accuracy: 67.16% | Test loss: 0.60058, Test acc: 66.08%
    Epoch: 810 | Loss: 0.59272, Accuracy: 67.20% | Test loss: 0.59996, Test acc: 66.18%
    Epoch: 820 | Loss: 0.59202, Accuracy: 67.21% | Test loss: 0.59935, Test acc: 66.24%
    Epoch: 830 | Loss: 0.59132, Accuracy: 67.33% | Test loss: 0.59874, Test acc: 66.39%
    Epoch: 840 | Loss: 0.59063, Accuracy: 67.36% | Test loss: 0.59815, Test acc: 66.39%
    Epoch: 850 | Loss: 0.58995, Accuracy: 67.36% | Test loss: 0.59757, Test acc: 66.44%
    Epoch: 860 | Loss: 0.58928, Accuracy: 67.46% | Test loss: 0.59700, Test acc: 66.49%
    Epoch: 870 | Loss: 0.58863, Accuracy: 67.54% | Test loss: 0.59643, Test acc: 66.60%
    Epoch: 880 | Loss: 0.58798, Accuracy: 67.60% | Test loss: 0.59587, Test acc: 66.65%
    Epoch: 890 | Loss: 0.58733, Accuracy: 67.56% | Test loss: 0.59533, Test acc: 66.49%
    Epoch: 900 | Loss: 0.58670, Accuracy: 67.63% | Test loss: 0.59479, Test acc: 66.55%
    Epoch: 910 | Loss: 0.58608, Accuracy: 67.69% | Test loss: 0.59426, Test acc: 66.70%
    Epoch: 920 | Loss: 0.58547, Accuracy: 67.66% | Test loss: 0.59375, Test acc: 66.80%
    Epoch: 930 | Loss: 0.58486, Accuracy: 67.69% | Test loss: 0.59324, Test acc: 66.80%
    Epoch: 940 | Loss: 0.58427, Accuracy: 67.69% | Test loss: 0.59274, Test acc: 66.91%
    Epoch: 950 | Loss: 0.58368, Accuracy: 67.74% | Test loss: 0.59224, Test acc: 67.01%
    Epoch: 960 | Loss: 0.58310, Accuracy: 67.81% | Test loss: 0.59176, Test acc: 67.01%
    Epoch: 970 | Loss: 0.58254, Accuracy: 67.83% | Test loss: 0.59128, Test acc: 67.06%
    Epoch: 980 | Loss: 0.58197, Accuracy: 67.92% | Test loss: 0.59082, Test acc: 67.06%
    Epoch: 990 | Loss: 0.58142, Accuracy: 67.95% | Test loss: 0.59036, Test acc: 67.17%
    Epoch: 1000 | Loss: 0.58088, Accuracy: 67.97% | Test loss: 0.58990, Test acc: 67.27%
    Epoch: 1010 | Loss: 0.58034, Accuracy: 68.05% | Test loss: 0.58946, Test acc: 67.37%
    Epoch: 1020 | Loss: 0.57981, Accuracy: 68.08% | Test loss: 0.58902, Test acc: 67.32%
    Epoch: 1030 | Loss: 0.57929, Accuracy: 68.07% | Test loss: 0.58860, Test acc: 67.27%
    Epoch: 1040 | Loss: 0.57877, Accuracy: 68.14% | Test loss: 0.58818, Test acc: 67.11%
    Epoch: 1050 | Loss: 0.57827, Accuracy: 68.27% | Test loss: 0.58776, Test acc: 67.11%
    Epoch: 1060 | Loss: 0.57777, Accuracy: 68.28% | Test loss: 0.58736, Test acc: 67.22%
    Epoch: 1070 | Loss: 0.57728, Accuracy: 68.40% | Test loss: 0.58696, Test acc: 67.22%
    Epoch: 1080 | Loss: 0.57679, Accuracy: 68.43% | Test loss: 0.58657, Test acc: 67.32%
    Epoch: 1090 | Loss: 0.57631, Accuracy: 68.40% | Test loss: 0.58618, Test acc: 67.32%
    Epoch: 1100 | Loss: 0.57584, Accuracy: 68.43% | Test loss: 0.58580, Test acc: 67.17%
    Epoch: 1110 | Loss: 0.57537, Accuracy: 68.44% | Test loss: 0.58543, Test acc: 67.11%
    Epoch: 1120 | Loss: 0.57491, Accuracy: 68.57% | Test loss: 0.58506, Test acc: 67.11%
    Epoch: 1130 | Loss: 0.57446, Accuracy: 68.54% | Test loss: 0.58470, Test acc: 67.17%
    Epoch: 1140 | Loss: 0.57401, Accuracy: 68.54% | Test loss: 0.58435, Test acc: 67.11%
    Epoch: 1150 | Loss: 0.57357, Accuracy: 68.54% | Test loss: 0.58400, Test acc: 67.11%
    Epoch: 1160 | Loss: 0.57313, Accuracy: 68.52% | Test loss: 0.58366, Test acc: 67.22%
    Epoch: 1170 | Loss: 0.57270, Accuracy: 68.47% | Test loss: 0.58332, Test acc: 67.22%
    Epoch: 1180 | Loss: 0.57228, Accuracy: 68.45% | Test loss: 0.58299, Test acc: 67.27%
    Epoch: 1190 | Loss: 0.57186, Accuracy: 68.45% | Test loss: 0.58267, Test acc: 67.11%
    Epoch: 1200 | Loss: 0.57145, Accuracy: 68.45% | Test loss: 0.58235, Test acc: 67.11%
    Epoch: 1210 | Loss: 0.57104, Accuracy: 68.47% | Test loss: 0.58204, Test acc: 67.27%
    Epoch: 1220 | Loss: 0.57064, Accuracy: 68.56% | Test loss: 0.58173, Test acc: 67.32%
    Epoch: 1230 | Loss: 0.57024, Accuracy: 68.61% | Test loss: 0.58143, Test acc: 67.42%
    Epoch: 1240 | Loss: 0.56985, Accuracy: 68.68% | Test loss: 0.58113, Test acc: 67.58%
    Epoch: 1250 | Loss: 0.56946, Accuracy: 68.74% | Test loss: 0.58084, Test acc: 67.58%
    Epoch: 1260 | Loss: 0.56908, Accuracy: 68.75% | Test loss: 0.58055, Test acc: 67.63%
    Epoch: 1270 | Loss: 0.56871, Accuracy: 68.83% | Test loss: 0.58027, Test acc: 67.58%
    Epoch: 1280 | Loss: 0.56834, Accuracy: 68.85% | Test loss: 0.57999, Test acc: 67.58%
    Epoch: 1290 | Loss: 0.56797, Accuracy: 68.81% | Test loss: 0.57972, Test acc: 67.58%
    Epoch: 1300 | Loss: 0.56761, Accuracy: 68.80% | Test loss: 0.57945, Test acc: 67.73%
    Epoch: 1310 | Loss: 0.56725, Accuracy: 68.81% | Test loss: 0.57919, Test acc: 67.79%
    Epoch: 1320 | Loss: 0.56690, Accuracy: 68.87% | Test loss: 0.57893, Test acc: 67.89%
    Epoch: 1330 | Loss: 0.56655, Accuracy: 68.94% | Test loss: 0.57868, Test acc: 67.84%
    Epoch: 1340 | Loss: 0.56621, Accuracy: 69.05% | Test loss: 0.57843, Test acc: 67.89%
    Epoch: 1350 | Loss: 0.56587, Accuracy: 69.08% | Test loss: 0.57818, Test acc: 67.94%
    Epoch: 1360 | Loss: 0.56553, Accuracy: 69.15% | Test loss: 0.57794, Test acc: 67.89%
    Epoch: 1370 | Loss: 0.56520, Accuracy: 69.20% | Test loss: 0.57771, Test acc: 67.79%
    Epoch: 1380 | Loss: 0.56488, Accuracy: 69.28% | Test loss: 0.57747, Test acc: 67.84%
    Epoch: 1390 | Loss: 0.56455, Accuracy: 69.28% | Test loss: 0.57724, Test acc: 67.84%
    Epoch: 1400 | Loss: 0.56424, Accuracy: 69.21% | Test loss: 0.57702, Test acc: 67.89%
    Epoch: 1410 | Loss: 0.56392, Accuracy: 69.30% | Test loss: 0.57679, Test acc: 67.99%
    Epoch: 1420 | Loss: 0.56361, Accuracy: 69.29% | Test loss: 0.57658, Test acc: 67.94%
    Epoch: 1430 | Loss: 0.56330, Accuracy: 69.34% | Test loss: 0.57636, Test acc: 67.84%
    Epoch: 1440 | Loss: 0.56300, Accuracy: 69.39% | Test loss: 0.57615, Test acc: 67.89%
    Epoch: 1450 | Loss: 0.56270, Accuracy: 69.41% | Test loss: 0.57594, Test acc: 67.79%
    Epoch: 1460 | Loss: 0.56240, Accuracy: 69.38% | Test loss: 0.57574, Test acc: 67.84%
    Epoch: 1470 | Loss: 0.56211, Accuracy: 69.39% | Test loss: 0.57553, Test acc: 67.84%
    Epoch: 1480 | Loss: 0.56182, Accuracy: 69.42% | Test loss: 0.57533, Test acc: 67.73%
    Epoch: 1490 | Loss: 0.56153, Accuracy: 69.47% | Test loss: 0.57514, Test acc: 67.68%
    Epoch: 1500 | Loss: 0.56125, Accuracy: 69.51% | Test loss: 0.57495, Test acc: 67.68%
    Epoch: 1510 | Loss: 0.56097, Accuracy: 69.52% | Test loss: 0.57476, Test acc: 67.58%
    Epoch: 1520 | Loss: 0.56069, Accuracy: 69.58% | Test loss: 0.57457, Test acc: 67.68%
    Epoch: 1530 | Loss: 0.56042, Accuracy: 69.61% | Test loss: 0.57439, Test acc: 67.79%
    Epoch: 1540 | Loss: 0.56015, Accuracy: 69.58% | Test loss: 0.57421, Test acc: 67.84%
    Epoch: 1550 | Loss: 0.55988, Accuracy: 69.61% | Test loss: 0.57403, Test acc: 67.79%
    Epoch: 1560 | Loss: 0.55961, Accuracy: 69.61% | Test loss: 0.57385, Test acc: 67.79%
    Epoch: 1570 | Loss: 0.55935, Accuracy: 69.65% | Test loss: 0.57368, Test acc: 67.79%
    Epoch: 1580 | Loss: 0.55909, Accuracy: 69.72% | Test loss: 0.57351, Test acc: 67.73%
    Epoch: 1590 | Loss: 0.55883, Accuracy: 69.72% | Test loss: 0.57334, Test acc: 67.73%
    Epoch: 1600 | Loss: 0.55858, Accuracy: 69.79% | Test loss: 0.57318, Test acc: 67.73%
    Epoch: 1610 | Loss: 0.55832, Accuracy: 69.85% | Test loss: 0.57302, Test acc: 67.68%
    Epoch: 1620 | Loss: 0.55808, Accuracy: 69.89% | Test loss: 0.57285, Test acc: 67.68%
    Epoch: 1630 | Loss: 0.55783, Accuracy: 69.91% | Test loss: 0.57270, Test acc: 67.63%
    Epoch: 1640 | Loss: 0.55758, Accuracy: 69.90% | Test loss: 0.57254, Test acc: 67.48%
    Epoch: 1650 | Loss: 0.55734, Accuracy: 69.94% | Test loss: 0.57239, Test acc: 67.48%
    Epoch: 1660 | Loss: 0.55710, Accuracy: 70.03% | Test loss: 0.57224, Test acc: 67.53%
    Epoch: 1670 | Loss: 0.55686, Accuracy: 70.07% | Test loss: 0.57209, Test acc: 67.63%
    Epoch: 1680 | Loss: 0.55663, Accuracy: 70.07% | Test loss: 0.57194, Test acc: 67.73%
    Epoch: 1690 | Loss: 0.55640, Accuracy: 70.07% | Test loss: 0.57179, Test acc: 67.84%
    Epoch: 1700 | Loss: 0.55617, Accuracy: 70.09% | Test loss: 0.57165, Test acc: 67.99%
    Epoch: 1710 | Loss: 0.55594, Accuracy: 70.10% | Test loss: 0.57151, Test acc: 67.99%
    Epoch: 1720 | Loss: 0.55571, Accuracy: 70.18% | Test loss: 0.57137, Test acc: 68.15%
    Epoch: 1730 | Loss: 0.55549, Accuracy: 70.16% | Test loss: 0.57123, Test acc: 68.20%
    Epoch: 1740 | Loss: 0.55527, Accuracy: 70.18% | Test loss: 0.57110, Test acc: 68.25%
    Epoch: 1750 | Loss: 0.55505, Accuracy: 70.22% | Test loss: 0.57097, Test acc: 68.35%
    Epoch: 1760 | Loss: 0.55483, Accuracy: 70.23% | Test loss: 0.57084, Test acc: 68.40%
    Epoch: 1770 | Loss: 0.55461, Accuracy: 70.26% | Test loss: 0.57070, Test acc: 68.46%
    Epoch: 1780 | Loss: 0.55440, Accuracy: 70.30% | Test loss: 0.57058, Test acc: 68.46%
    Epoch: 1790 | Loss: 0.55419, Accuracy: 70.31% | Test loss: 0.57045, Test acc: 68.51%
    Epoch: 1800 | Loss: 0.55398, Accuracy: 70.34% | Test loss: 0.57032, Test acc: 68.40%
    Epoch: 1810 | Loss: 0.55377, Accuracy: 70.38% | Test loss: 0.57020, Test acc: 68.40%
    Epoch: 1820 | Loss: 0.55356, Accuracy: 70.41% | Test loss: 0.57008, Test acc: 68.35%
    Epoch: 1830 | Loss: 0.55336, Accuracy: 70.48% | Test loss: 0.56996, Test acc: 68.46%
    Epoch: 1840 | Loss: 0.55315, Accuracy: 70.49% | Test loss: 0.56984, Test acc: 68.46%
    Epoch: 1850 | Loss: 0.55295, Accuracy: 70.50% | Test loss: 0.56972, Test acc: 68.46%
    Epoch: 1860 | Loss: 0.55275, Accuracy: 70.50% | Test loss: 0.56961, Test acc: 68.46%
    Epoch: 1870 | Loss: 0.55255, Accuracy: 70.49% | Test loss: 0.56949, Test acc: 68.56%
    Epoch: 1880 | Loss: 0.55235, Accuracy: 70.52% | Test loss: 0.56938, Test acc: 68.56%
    Epoch: 1890 | Loss: 0.55216, Accuracy: 70.57% | Test loss: 0.56927, Test acc: 68.56%
    Epoch: 1900 | Loss: 0.55197, Accuracy: 70.61% | Test loss: 0.56915, Test acc: 68.61%
    Epoch: 1910 | Loss: 0.55177, Accuracy: 70.63% | Test loss: 0.56905, Test acc: 68.71%
    Epoch: 1920 | Loss: 0.55158, Accuracy: 70.58% | Test loss: 0.56894, Test acc: 68.77%
    Epoch: 1930 | Loss: 0.55139, Accuracy: 70.58% | Test loss: 0.56883, Test acc: 68.77%
    Epoch: 1940 | Loss: 0.55121, Accuracy: 70.62% | Test loss: 0.56873, Test acc: 68.77%
    Epoch: 1950 | Loss: 0.55102, Accuracy: 70.65% | Test loss: 0.56862, Test acc: 68.87%
    Epoch: 1960 | Loss: 0.55084, Accuracy: 70.63% | Test loss: 0.56852, Test acc: 68.87%
    Epoch: 1970 | Loss: 0.55066, Accuracy: 70.66% | Test loss: 0.56842, Test acc: 68.87%
    Epoch: 1980 | Loss: 0.55047, Accuracy: 70.69% | Test loss: 0.56832, Test acc: 68.92%
    Epoch: 1990 | Loss: 0.55029, Accuracy: 70.72% | Test loss: 0.56822, Test acc: 68.87%
    

Regularization & Hyperparameter tuning
0.01 - 68.71%
0.001 - 64.79%

More feature engineering - The last experiment I want to run is an additional feature engineering step. 
The data I have passed to the model previously is in the style of fighter_1_stat in one column and fighter_b_stat in another. I want to guide my model slightly by creating new columns formed from the difference in the stats between fighter_a and fighter_b to see if this helps the accuracy.


```python
X_differences = X.copy() 
X_differences['height_difference'] = X_differences.fighter_a_height - X_differences.fighter_b_height
X_differences['weight_difference'] = X_differences.fighter_a_weight - X_differences.fighter_b_weight
X_differences['reach_difference'] = X_differences.fighter_a_reach - X_differences.fighter_b_reach
X_differences['SLpM_difference'] = X_differences.fighter_a_SLpM - X_differences.fighter_b_SLpM
X_differences['str_acc_difference'] = X_differences.fighter_a_str_acc - X_differences.fighter_b_str_acc
X_differences['SApM_difference'] = X_differences.fighter_a_SApM - X_differences.fighter_b_SApM
X_differences['str_def_difference'] = X_differences.fighter_a_str_def - X_differences.fighter_b_str_def
X_differences['TD_avg_difference'] = X_differences.fighter_a_TD_avg - X_differences.fighter_b_TD_avg
X_differences['TD_acc_difference'] = X_differences.fighter_a_TD_acc - X_differences.fighter_b_TD_acc
X_differences['TD_def_difference'] = X_differences.fighter_a_TD_def - X_differences.fighter_b_TD_def
X_differences['sub_avg_difference'] = X_differences.fighter_a_sub_avg - X_differences.fighter_b_sub_avg
X_differences['age_difference'] = X_differences.fighter_a_age - X_differences.fighter_b_age
X_differences
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
      <th>height_difference</th>
      <th>weight_difference</th>
      <th>reach_difference</th>
      <th>SLpM_difference</th>
      <th>str_acc_difference</th>
      <th>SApM_difference</th>
      <th>str_def_difference</th>
      <th>TD_avg_difference</th>
      <th>TD_acc_difference</th>
      <th>TD_def_difference</th>
      <th>sub_avg_difference</th>
      <th>age_difference</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>68.0</td>
      <td>69.0</td>
      <td>135.0</td>
      <td>135.0</td>
      <td>69.00000</td>
      <td>68.000000</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>3.21</td>
      <td>5.24</td>
      <td>40.0</td>
      <td>40.0</td>
      <td>2.79</td>
      <td>6.33</td>
      <td>56.0</td>
      <td>57.0</td>
      <td>0.90</td>
      <td>0.16</td>
      <td>30.0</td>
      <td>50.0</td>
      <td>78.0</td>
      <td>76.0</td>
      <td>0.1</td>
      <td>0.2</td>
      <td>43.0</td>
      <td>36.000000</td>
      <td>-1.0</td>
      <td>0.0</td>
      <td>1.000000</td>
      <td>-2.03</td>
      <td>0.0</td>
      <td>-3.54</td>
      <td>-1.0</td>
      <td>0.74</td>
      <td>-20.0</td>
      <td>2.0</td>
      <td>-0.1</td>
      <td>7.000000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>70.0</td>
      <td>71.0</td>
      <td>170.0</td>
      <td>170.0</td>
      <td>72.00000</td>
      <td>72.000000</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>2.69</td>
      <td>3.29</td>
      <td>43.0</td>
      <td>46.0</td>
      <td>3.13</td>
      <td>3.63</td>
      <td>51.0</td>
      <td>58.0</td>
      <td>2.53</td>
      <td>1.09</td>
      <td>36.0</td>
      <td>34.0</td>
      <td>72.0</td>
      <td>46.0</td>
      <td>0.3</td>
      <td>1.3</td>
      <td>44.0</td>
      <td>41.000000</td>
      <td>-1.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>-0.60</td>
      <td>-3.0</td>
      <td>-0.50</td>
      <td>-7.0</td>
      <td>1.44</td>
      <td>2.0</td>
      <td>26.0</td>
      <td>-1.0</td>
      <td>3.000000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>70.0</td>
      <td>68.0</td>
      <td>155.0</td>
      <td>155.0</td>
      <td>73.00000</td>
      <td>71.000000</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>5.13</td>
      <td>3.65</td>
      <td>52.0</td>
      <td>53.0</td>
      <td>3.70</td>
      <td>1.77</td>
      <td>41.0</td>
      <td>57.0</td>
      <td>0.98</td>
      <td>0.40</td>
      <td>25.0</td>
      <td>25.0</td>
      <td>56.0</td>
      <td>30.0</td>
      <td>1.6</td>
      <td>0.4</td>
      <td>29.0</td>
      <td>39.000000</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>2.000000</td>
      <td>1.48</td>
      <td>-1.0</td>
      <td>1.93</td>
      <td>-16.0</td>
      <td>0.58</td>
      <td>0.0</td>
      <td>26.0</td>
      <td>1.2</td>
      <td>-10.000000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>74.0</td>
      <td>73.0</td>
      <td>265.0</td>
      <td>265.0</td>
      <td>75.00000</td>
      <td>71.982162</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>2.08</td>
      <td>1.03</td>
      <td>46.0</td>
      <td>31.0</td>
      <td>1.52</td>
      <td>3.01</td>
      <td>59.0</td>
      <td>55.0</td>
      <td>1.83</td>
      <td>0.00</td>
      <td>41.0</td>
      <td>0.0</td>
      <td>66.0</td>
      <td>57.0</td>
      <td>0.1</td>
      <td>0.0</td>
      <td>38.0</td>
      <td>42.000000</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>3.017838</td>
      <td>1.05</td>
      <td>15.0</td>
      <td>-1.49</td>
      <td>4.0</td>
      <td>1.83</td>
      <td>41.0</td>
      <td>9.0</td>
      <td>0.1</td>
      <td>-4.000000</td>
    </tr>
    <tr>
      <th>5</th>
      <td>76.0</td>
      <td>83.0</td>
      <td>265.0</td>
      <td>265.0</td>
      <td>80.00000</td>
      <td>84.000000</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>3.67</td>
      <td>3.12</td>
      <td>64.0</td>
      <td>47.0</td>
      <td>2.44</td>
      <td>4.04</td>
      <td>57.0</td>
      <td>46.0</td>
      <td>1.42</td>
      <td>0.56</td>
      <td>55.0</td>
      <td>46.0</td>
      <td>73.0</td>
      <td>55.0</td>
      <td>0.8</td>
      <td>1.7</td>
      <td>44.0</td>
      <td>36.000000</td>
      <td>-7.0</td>
      <td>0.0</td>
      <td>-4.000000</td>
      <td>0.55</td>
      <td>17.0</td>
      <td>-1.60</td>
      <td>11.0</td>
      <td>0.86</td>
      <td>9.0</td>
      <td>18.0</td>
      <td>-0.9</td>
      <td>8.000000</td>
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
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>7514</th>
      <td>80.0</td>
      <td>75.0</td>
      <td>265.0</td>
      <td>260.0</td>
      <td>80.00000</td>
      <td>71.982162</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>4.23</td>
      <td>2.60</td>
      <td>41.0</td>
      <td>36.0</td>
      <td>2.61</td>
      <td>8.80</td>
      <td>61.0</td>
      <td>40.0</td>
      <td>0.11</td>
      <td>0.00</td>
      <td>100.0</td>
      <td>0.0</td>
      <td>75.0</td>
      <td>90.0</td>
      <td>0.1</td>
      <td>0.0</td>
      <td>48.0</td>
      <td>46.000000</td>
      <td>5.0</td>
      <td>5.0</td>
      <td>8.017838</td>
      <td>1.63</td>
      <td>5.0</td>
      <td>-6.19</td>
      <td>21.0</td>
      <td>0.11</td>
      <td>100.0</td>
      <td>-15.0</td>
      <td>0.1</td>
      <td>2.000000</td>
    </tr>
    <tr>
      <th>7515</th>
      <td>75.0</td>
      <td>72.0</td>
      <td>235.0</td>
      <td>265.0</td>
      <td>72.13694</td>
      <td>71.982162</td>
      <td>1.0</td>
      <td>2.0</td>
      <td>0.76</td>
      <td>1.35</td>
      <td>83.0</td>
      <td>30.0</td>
      <td>2.12</td>
      <td>3.55</td>
      <td>30.0</td>
      <td>38.0</td>
      <td>4.55</td>
      <td>1.07</td>
      <td>100.0</td>
      <td>33.0</td>
      <td>0.0</td>
      <td>66.0</td>
      <td>2.3</td>
      <td>0.0</td>
      <td>56.0</td>
      <td>38.544971</td>
      <td>3.0</td>
      <td>-30.0</td>
      <td>0.154778</td>
      <td>-0.59</td>
      <td>53.0</td>
      <td>-1.43</td>
      <td>-8.0</td>
      <td>3.48</td>
      <td>67.0</td>
      <td>-66.0</td>
      <td>2.3</td>
      <td>17.455029</td>
    </tr>
    <tr>
      <th>7516</th>
      <td>71.0</td>
      <td>70.0</td>
      <td>155.0</td>
      <td>155.0</td>
      <td>76.00000</td>
      <td>71.000000</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>4.94</td>
      <td>1.95</td>
      <td>45.0</td>
      <td>31.0</td>
      <td>4.41</td>
      <td>2.51</td>
      <td>55.0</td>
      <td>63.0</td>
      <td>0.39</td>
      <td>4.08</td>
      <td>35.0</td>
      <td>53.0</td>
      <td>67.0</td>
      <td>92.0</td>
      <td>0.9</td>
      <td>0.8</td>
      <td>40.0</td>
      <td>41.000000</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>5.000000</td>
      <td>2.99</td>
      <td>14.0</td>
      <td>1.90</td>
      <td>-8.0</td>
      <td>-3.69</td>
      <td>-18.0</td>
      <td>-25.0</td>
      <td>0.1</td>
      <td>-1.000000</td>
    </tr>
    <tr>
      <th>7517</th>
      <td>72.0</td>
      <td>74.0</td>
      <td>185.0</td>
      <td>185.0</td>
      <td>74.00000</td>
      <td>76.000000</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>2.93</td>
      <td>2.65</td>
      <td>50.0</td>
      <td>47.0</td>
      <td>2.90</td>
      <td>2.58</td>
      <td>57.0</td>
      <td>54.0</td>
      <td>1.45</td>
      <td>3.55</td>
      <td>34.0</td>
      <td>54.0</td>
      <td>59.0</td>
      <td>62.0</td>
      <td>0.8</td>
      <td>1.2</td>
      <td>43.0</td>
      <td>41.000000</td>
      <td>-2.0</td>
      <td>0.0</td>
      <td>-2.000000</td>
      <td>0.28</td>
      <td>3.0</td>
      <td>0.32</td>
      <td>3.0</td>
      <td>-2.10</td>
      <td>-20.0</td>
      <td>-3.0</td>
      <td>-0.4</td>
      <td>2.000000</td>
    </tr>
    <tr>
      <th>7518</th>
      <td>75.0</td>
      <td>75.0</td>
      <td>235.0</td>
      <td>205.0</td>
      <td>76.00000</td>
      <td>79.000000</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>2.41</td>
      <td>2.78</td>
      <td>44.0</td>
      <td>64.0</td>
      <td>3.02</td>
      <td>0.52</td>
      <td>55.0</td>
      <td>43.0</td>
      <td>1.01</td>
      <td>5.14</td>
      <td>23.0</td>
      <td>55.0</td>
      <td>45.0</td>
      <td>75.0</td>
      <td>0.1</td>
      <td>2.4</td>
      <td>43.0</td>
      <td>33.000000</td>
      <td>0.0</td>
      <td>30.0</td>
      <td>-3.000000</td>
      <td>-0.37</td>
      <td>-20.0</td>
      <td>2.50</td>
      <td>12.0</td>
      <td>-4.13</td>
      <td>-32.0</td>
      <td>-30.0</td>
      <td>-2.3</td>
      <td>10.000000</td>
    </tr>
  </tbody>
</table>
<p>7377 rows × 38 columns</p>
</div>




```python
# create a list of columns to drop
columns_to_drop = X.columns
columns_to_drop = columns_to_drop.drop(['fighter_a_stance', 'fighter_b_stance'])
columns_to_drop
X_differences.drop(columns_to_drop, axis=1, inplace=True)
X_differences
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
      <th>fighter_a_stance</th>
      <th>fighter_b_stance</th>
      <th>height_difference</th>
      <th>weight_difference</th>
      <th>reach_difference</th>
      <th>SLpM_difference</th>
      <th>str_acc_difference</th>
      <th>SApM_difference</th>
      <th>str_def_difference</th>
      <th>TD_avg_difference</th>
      <th>TD_acc_difference</th>
      <th>TD_def_difference</th>
      <th>sub_avg_difference</th>
      <th>age_difference</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.0</td>
      <td>1.0</td>
      <td>-1.0</td>
      <td>0.0</td>
      <td>1.000000</td>
      <td>-2.03</td>
      <td>0.0</td>
      <td>-3.54</td>
      <td>-1.0</td>
      <td>0.74</td>
      <td>-20.0</td>
      <td>2.0</td>
      <td>-0.1</td>
      <td>7.000000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>-1.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>-0.60</td>
      <td>-3.0</td>
      <td>-0.50</td>
      <td>-7.0</td>
      <td>1.44</td>
      <td>2.0</td>
      <td>26.0</td>
      <td>-1.0</td>
      <td>3.000000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1.0</td>
      <td>0.0</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>2.000000</td>
      <td>1.48</td>
      <td>-1.0</td>
      <td>1.93</td>
      <td>-16.0</td>
      <td>0.58</td>
      <td>0.0</td>
      <td>26.0</td>
      <td>1.2</td>
      <td>-10.000000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>3.017838</td>
      <td>1.05</td>
      <td>15.0</td>
      <td>-1.49</td>
      <td>4.0</td>
      <td>1.83</td>
      <td>41.0</td>
      <td>9.0</td>
      <td>0.1</td>
      <td>-4.000000</td>
    </tr>
    <tr>
      <th>5</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>-7.0</td>
      <td>0.0</td>
      <td>-4.000000</td>
      <td>0.55</td>
      <td>17.0</td>
      <td>-1.60</td>
      <td>11.0</td>
      <td>0.86</td>
      <td>9.0</td>
      <td>18.0</td>
      <td>-0.9</td>
      <td>8.000000</td>
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
    </tr>
    <tr>
      <th>7514</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>5.0</td>
      <td>5.0</td>
      <td>8.017838</td>
      <td>1.63</td>
      <td>5.0</td>
      <td>-6.19</td>
      <td>21.0</td>
      <td>0.11</td>
      <td>100.0</td>
      <td>-15.0</td>
      <td>0.1</td>
      <td>2.000000</td>
    </tr>
    <tr>
      <th>7515</th>
      <td>1.0</td>
      <td>2.0</td>
      <td>3.0</td>
      <td>-30.0</td>
      <td>0.154778</td>
      <td>-0.59</td>
      <td>53.0</td>
      <td>-1.43</td>
      <td>-8.0</td>
      <td>3.48</td>
      <td>67.0</td>
      <td>-66.0</td>
      <td>2.3</td>
      <td>17.455029</td>
    </tr>
    <tr>
      <th>7516</th>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>5.000000</td>
      <td>2.99</td>
      <td>14.0</td>
      <td>1.90</td>
      <td>-8.0</td>
      <td>-3.69</td>
      <td>-18.0</td>
      <td>-25.0</td>
      <td>0.1</td>
      <td>-1.000000</td>
    </tr>
    <tr>
      <th>7517</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>-2.0</td>
      <td>0.0</td>
      <td>-2.000000</td>
      <td>0.28</td>
      <td>3.0</td>
      <td>0.32</td>
      <td>3.0</td>
      <td>-2.10</td>
      <td>-20.0</td>
      <td>-3.0</td>
      <td>-0.4</td>
      <td>2.000000</td>
    </tr>
    <tr>
      <th>7518</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>30.0</td>
      <td>-3.000000</td>
      <td>-0.37</td>
      <td>-20.0</td>
      <td>2.50</td>
      <td>12.0</td>
      <td>-4.13</td>
      <td>-32.0</td>
      <td>-30.0</td>
      <td>-2.3</td>
      <td>10.000000</td>
    </tr>
  </tbody>
</table>
<p>7377 rows × 14 columns</p>
</div>




```python
# Rescale the data
scaler_v1 = StandardScaler()

X_differences_scaled = pd.DataFrame(scaler_v1.fit_transform(X_differences), columns = X_differences.columns)
X_differences_scaled
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
      <th>height_difference</th>
      <th>weight_difference</th>
      <th>reach_difference</th>
      <th>SLpM_difference</th>
      <th>str_acc_difference</th>
      <th>SApM_difference</th>
      <th>str_def_difference</th>
      <th>TD_avg_difference</th>
      <th>TD_acc_difference</th>
      <th>TD_def_difference</th>
      <th>sub_avg_difference</th>
      <th>age_difference</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-0.659640</td>
      <td>-0.373257</td>
      <td>-0.931184</td>
      <td>-0.906098</td>
      <td>-0.757287</td>
      <td>-1.013374</td>
      <td>-1.753881</td>
      <td>0.279421</td>
      <td>-0.148090</td>
      <td>1.353419</td>
      <td>-0.445337</td>
      <td>-0.261940</td>
      <td>-0.400209</td>
      <td>1.855950</td>
      <td>0.171667</td>
      <td>0.408279</td>
      <td>-0.549418</td>
      <td>-0.995601</td>
      <td>-0.475411</td>
      <td>0.656339</td>
      <td>0.859495</td>
      <td>0.810857</td>
      <td>-0.722890</td>
      <td>-0.495254</td>
      <td>0.603610</td>
      <td>-0.405001</td>
      <td>-0.398840</td>
      <td>-0.029497</td>
      <td>0.260052</td>
      <td>-1.376109</td>
      <td>-0.101290</td>
      <td>-1.989788</td>
      <td>-0.270277</td>
      <td>0.339866</td>
      <td>-0.828412</td>
      <td>-0.061198</td>
      <td>-0.136728</td>
      <td>1.135309</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-0.093005</td>
      <td>0.198802</td>
      <td>0.036010</td>
      <td>0.047716</td>
      <td>-0.033222</td>
      <td>0.004272</td>
      <td>0.308307</td>
      <td>0.279421</td>
      <td>-0.544252</td>
      <td>0.016093</td>
      <td>-0.120643</td>
      <td>0.280671</td>
      <td>-0.122237</td>
      <td>0.116667</td>
      <td>-0.338397</td>
      <td>0.493946</td>
      <td>0.739132</td>
      <td>-0.282130</td>
      <td>-0.153899</td>
      <td>-0.092313</td>
      <td>0.567108</td>
      <td>-0.441215</td>
      <td>-0.458027</td>
      <td>0.860401</td>
      <td>0.760486</td>
      <td>0.386795</td>
      <td>-0.398840</td>
      <td>-0.029497</td>
      <td>-0.047497</td>
      <td>-0.468372</td>
      <td>-0.350723</td>
      <td>-0.195178</td>
      <td>-0.796262</td>
      <td>0.734571</td>
      <td>-0.032528</td>
      <td>0.758945</td>
      <td>-0.991268</td>
      <td>0.426315</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-0.093005</td>
      <td>-0.659287</td>
      <td>-0.378502</td>
      <td>-0.361061</td>
      <td>0.208133</td>
      <td>-0.250140</td>
      <td>0.308307</td>
      <td>-1.777205</td>
      <td>1.314660</td>
      <td>0.262984</td>
      <td>0.853438</td>
      <td>0.913718</td>
      <td>0.343775</td>
      <td>-1.081505</td>
      <td>-1.358526</td>
      <td>0.408279</td>
      <td>-0.486177</td>
      <td>-0.811480</td>
      <td>-0.743338</td>
      <td>-0.513429</td>
      <td>-0.212588</td>
      <td>-1.108987</td>
      <td>1.263586</td>
      <td>-0.248771</td>
      <td>-1.592649</td>
      <td>0.070077</td>
      <td>0.770423</td>
      <td>-0.029497</td>
      <td>0.567601</td>
      <td>0.851972</td>
      <td>-0.184435</td>
      <td>1.239330</td>
      <td>-1.585239</td>
      <td>0.249648</td>
      <td>-0.104881</td>
      <td>0.758945</td>
      <td>1.097608</td>
      <td>-1.877916</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1.040266</td>
      <td>0.770862</td>
      <td>2.661251</td>
      <td>2.636643</td>
      <td>0.690843</td>
      <td>-0.000267</td>
      <td>0.308307</td>
      <td>0.279421</td>
      <td>-1.008979</td>
      <td>-1.533832</td>
      <td>0.204050</td>
      <td>-1.075856</td>
      <td>-1.438516</td>
      <td>-0.282724</td>
      <td>0.477706</td>
      <td>0.236945</td>
      <td>0.185767</td>
      <td>-1.118349</td>
      <td>0.114028</td>
      <td>-1.683198</td>
      <td>0.274722</td>
      <td>0.017878</td>
      <td>-0.722890</td>
      <td>-0.741737</td>
      <td>-0.180768</td>
      <td>0.545154</td>
      <td>0.380669</td>
      <td>-0.029497</td>
      <td>0.880636</td>
      <td>0.579016</td>
      <td>1.145875</td>
      <td>-0.779607</td>
      <td>0.168044</td>
      <td>0.954478</td>
      <td>1.378357</td>
      <td>0.178011</td>
      <td>0.053170</td>
      <td>-0.814425</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1.606901</td>
      <td>3.631160</td>
      <td>2.661251</td>
      <td>2.636643</td>
      <td>1.897618</td>
      <td>3.057209</td>
      <td>0.308307</td>
      <td>0.279421</td>
      <td>0.202360</td>
      <td>-0.100494</td>
      <td>2.152213</td>
      <td>0.371107</td>
      <td>-0.686356</td>
      <td>0.380781</td>
      <td>0.273680</td>
      <td>-0.534056</td>
      <td>-0.138347</td>
      <td>-0.688732</td>
      <td>0.864223</td>
      <td>0.469176</td>
      <td>0.615839</td>
      <td>-0.065594</td>
      <td>0.204132</td>
      <td>1.353366</td>
      <td>0.760486</td>
      <td>-0.405001</td>
      <td>-2.737366</td>
      <td>-0.029497</td>
      <td>-1.277692</td>
      <td>0.261626</td>
      <td>1.312164</td>
      <td>-0.844543</td>
      <td>0.781693</td>
      <td>0.407530</td>
      <td>0.220708</td>
      <td>0.485564</td>
      <td>-0.896319</td>
      <td>1.312557</td>
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
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>7372</th>
      <td>2.740172</td>
      <td>1.342922</td>
      <td>2.661251</td>
      <td>2.500383</td>
      <td>1.897618</td>
      <td>-0.000267</td>
      <td>0.308307</td>
      <td>0.279421</td>
      <td>0.628996</td>
      <td>-0.457114</td>
      <td>-0.337106</td>
      <td>-0.623680</td>
      <td>-0.547370</td>
      <td>3.447072</td>
      <td>0.681731</td>
      <td>-1.048057</td>
      <td>-1.173930</td>
      <td>-1.118349</td>
      <td>3.275563</td>
      <td>-1.683198</td>
      <td>0.713301</td>
      <td>1.395157</td>
      <td>-0.722890</td>
      <td>-0.741737</td>
      <td>1.387988</td>
      <td>1.178591</td>
      <td>1.939687</td>
      <td>0.299824</td>
      <td>2.418380</td>
      <td>0.947189</td>
      <td>0.314431</td>
      <td>-3.554169</td>
      <td>1.658334</td>
      <td>-0.015368</td>
      <td>3.512772</td>
      <td>-0.642132</td>
      <td>0.053170</td>
      <td>0.249066</td>
    </tr>
    <tr>
      <th>7373</th>
      <td>1.323584</td>
      <td>0.484832</td>
      <td>1.832228</td>
      <td>2.636643</td>
      <td>-0.000171</td>
      <td>-0.000267</td>
      <td>0.308307</td>
      <td>2.336047</td>
      <td>-2.014620</td>
      <td>-1.314374</td>
      <td>4.208607</td>
      <td>-1.166291</td>
      <td>-0.947977</td>
      <td>0.065133</td>
      <td>-2.480667</td>
      <td>-1.219391</td>
      <td>2.335985</td>
      <td>-0.297473</td>
      <td>3.275563</td>
      <td>-0.139104</td>
      <td>-2.941526</td>
      <td>0.393499</td>
      <td>2.190608</td>
      <td>-0.741737</td>
      <td>2.642994</td>
      <td>-0.001981</td>
      <td>1.160178</td>
      <td>-2.005425</td>
      <td>0.000105</td>
      <td>-0.462025</td>
      <td>4.305360</td>
      <td>-0.744187</td>
      <td>-0.883926</td>
      <td>1.884854</td>
      <td>2.318946</td>
      <td>-2.384934</td>
      <td>2.142046</td>
      <td>2.988447</td>
    </tr>
    <tr>
      <th>7374</th>
      <td>0.190313</td>
      <td>-0.087228</td>
      <td>-0.378502</td>
      <td>-0.361061</td>
      <td>0.932198</td>
      <td>-0.250140</td>
      <td>0.308307</td>
      <td>-1.777205</td>
      <td>1.169909</td>
      <td>-0.902889</td>
      <td>0.095819</td>
      <td>-1.075856</td>
      <td>0.924246</td>
      <td>-0.604813</td>
      <td>0.069654</td>
      <td>0.922280</td>
      <td>-0.952584</td>
      <td>2.011719</td>
      <td>-0.207484</td>
      <td>0.796711</td>
      <td>0.323453</td>
      <td>1.478628</td>
      <td>0.336564</td>
      <td>0.244194</td>
      <td>0.132983</td>
      <td>0.386795</td>
      <td>0.380669</td>
      <td>-0.029497</td>
      <td>1.490248</td>
      <td>1.810492</td>
      <td>1.062731</td>
      <td>1.221620</td>
      <td>-0.883926</td>
      <td>-2.158051</td>
      <td>-0.756058</td>
      <td>-0.983858</td>
      <td>0.053170</td>
      <td>-0.282679</td>
    </tr>
    <tr>
      <th>7375</th>
      <td>0.473631</td>
      <td>1.056892</td>
      <td>0.450522</td>
      <td>0.456494</td>
      <td>0.449488</td>
      <td>1.021917</td>
      <td>0.308307</td>
      <td>0.279421</td>
      <td>-0.361408</td>
      <td>-0.422824</td>
      <td>0.636976</td>
      <td>0.371107</td>
      <td>-0.310277</td>
      <td>-0.559720</td>
      <td>0.273680</td>
      <td>0.151279</td>
      <td>-0.114632</td>
      <td>1.605117</td>
      <td>-0.261070</td>
      <td>0.843502</td>
      <td>-0.066395</td>
      <td>0.226557</td>
      <td>0.204132</td>
      <td>0.737159</td>
      <td>0.603610</td>
      <td>0.386795</td>
      <td>-0.788594</td>
      <td>-0.029497</td>
      <td>-0.662595</td>
      <td>0.090235</td>
      <td>0.148143</td>
      <td>0.288895</td>
      <td>0.080380</td>
      <td>-1.261508</td>
      <td>-0.828412</td>
      <td>-0.232061</td>
      <td>-0.421575</td>
      <td>0.249066</td>
    </tr>
    <tr>
      <th>7376</th>
      <td>1.323584</td>
      <td>1.342922</td>
      <td>1.832228</td>
      <td>1.001531</td>
      <td>0.932198</td>
      <td>1.785151</td>
      <td>0.308307</td>
      <td>0.279421</td>
      <td>-0.757569</td>
      <td>-0.333669</td>
      <td>-0.012412</td>
      <td>1.908505</td>
      <td>-0.212169</td>
      <td>-1.886729</td>
      <td>0.069654</td>
      <td>-0.791056</td>
      <td>-0.462461</td>
      <td>2.824923</td>
      <td>-0.850509</td>
      <td>0.890292</td>
      <td>-0.748629</td>
      <td>0.769121</td>
      <td>-0.722890</td>
      <td>2.216056</td>
      <td>0.603610</td>
      <td>-0.880078</td>
      <td>-0.009085</td>
      <td>1.946430</td>
      <td>-0.970143</td>
      <td>-0.322373</td>
      <td>-1.764177</td>
      <td>1.575819</td>
      <td>0.869357</td>
      <td>-2.406152</td>
      <td>-1.262530</td>
      <td>-1.154721</td>
      <td>-2.225604</td>
      <td>1.667054</td>
    </tr>
  </tbody>
</table>
<p>7377 rows × 38 columns</p>
</div>




```python
# rebalance the data
smote_v1 = SMOTE()

# Balancing the data
X_differences_resampled, y_differences_resampled = smote_v1.fit_resample(X_differences_scaled, y)
```


```python
# Retrain the model
X_differences_ = torch.tensor(X_differences_resampled.values, dtype=torch.float32)
y_differences_ = torch.tensor(y_differences_resampled, dtype=torch.float32).reshape(-1)

X_train, X_test, y_train, y_test = train_test_split(X_differences_, y_differences_, test_size=0.2, random_state=13)
```


```python
X_train.shape
```




    torch.Size([7747, 38])




```python
# build a class for our model

class ufc_fighter_v3(nn.Module):
    def __init__(self):
        super().__init__()
        
        # create three layer neural network
        self.layer_1 = nn.Linear(in_features=38, out_features=152)
        self.layer_2 = nn.Linear(in_features=152, out_features=252)
        self.layer_3 = nn.Linear(in_features=252, out_features=1)
        self.relu = nn.ReLU()

    
    def forward(self, x):
        return self.layer_3(self.relu(self.layer_2(self.relu(self.layer_1(x)))))
    
```


```python
# instantiate our model
model_3 = ufc_fighter_v3().to(device)
model_3
```




    ufc_fighter_v3(
      (layer_1): Linear(in_features=38, out_features=152, bias=True)
      (layer_2): Linear(in_features=152, out_features=252, bias=True)
      (layer_3): Linear(in_features=252, out_features=1, bias=True)
      (relu): ReLU()
    )




```python
# Set up a loss function
loss_fn_3 = nn.BCEWithLogitsLoss() # BCEWithLogitsLoss = sigmoid built-in

# Create an optimizer
optimizer_3 = torch.optim.SGD(params=model_3.parameters(), 
                            lr=0.01)
```


```python
# Lets build our training loop

# number of epochs we want to run our training loop for
epochs = 5000

# transfer our data onto the target device
X_train, y_train = X_train.to(device), y_train.to(device)
X_test, y_test = X_test.to(device), y_test.to(device)

# set up the training loop
for epoch in range(epochs):
    model_3.train()
    
    # forward pass
    y_logits = model_3(X_train).squeeze() # What is the purpose of squeeze?
    y_pred = torch.round(torch.sigmoid(y_logits))

    
    # calculate the loss/accuracy
    loss = loss_fn_3(y_logits, y_train)
    acc = accuracy_fn(y_true=y_train, y_pred=y_pred)
    
    # optimizer zero grad
    optimizer_3.zero_grad() # Why do we zero grad?
    
    # calculate the loss
    loss.backward()
    
    # optimizer
    optimizer_3.step()
    
    #  
    model_3.eval()
    with torch.inference_mode():
        # 1. Forward pass
        test_logits = model_3(X_test).squeeze() 
        test_pred = torch.round(torch.sigmoid(test_logits))
        # 2. Caculate loss/accuracy
        test_loss = loss_fn_3(test_logits,
                            y_test)
        test_acc = accuracy_fn(y_true=y_test,
                               y_pred=test_pred)

    # Print out what's happening every 10 epochs
    if epoch % 10 == 0:
        print(f"Epoch: {epoch} | Loss: {loss:.5f}, Accuracy: {acc:.2f}% | Test loss: {test_loss:.5f}, Test acc: {test_acc:.2f}%")
    
```

    Epoch: 0 | Loss: 0.46271, Accuracy: 77.80% | Test loss: 0.53575, Test acc: 72.38%
    Epoch: 10 | Loss: 0.46261, Accuracy: 77.82% | Test loss: 0.53574, Test acc: 72.38%
    Epoch: 20 | Loss: 0.46251, Accuracy: 77.85% | Test loss: 0.53572, Test acc: 72.43%
    Epoch: 30 | Loss: 0.46241, Accuracy: 77.84% | Test loss: 0.53571, Test acc: 72.33%
    Epoch: 40 | Loss: 0.46231, Accuracy: 77.81% | Test loss: 0.53569, Test acc: 72.33%
    Epoch: 50 | Loss: 0.46221, Accuracy: 77.81% | Test loss: 0.53568, Test acc: 72.33%
    Epoch: 60 | Loss: 0.46211, Accuracy: 77.84% | Test loss: 0.53566, Test acc: 72.33%
    Epoch: 70 | Loss: 0.46201, Accuracy: 77.86% | Test loss: 0.53565, Test acc: 72.33%
    Epoch: 80 | Loss: 0.46191, Accuracy: 77.88% | Test loss: 0.53563, Test acc: 72.33%
    Epoch: 90 | Loss: 0.46181, Accuracy: 77.89% | Test loss: 0.53562, Test acc: 72.38%
    Epoch: 100 | Loss: 0.46170, Accuracy: 77.88% | Test loss: 0.53560, Test acc: 72.38%
    Epoch: 110 | Loss: 0.46160, Accuracy: 77.89% | Test loss: 0.53558, Test acc: 72.38%
    Epoch: 120 | Loss: 0.46150, Accuracy: 77.90% | Test loss: 0.53557, Test acc: 72.38%
    Epoch: 130 | Loss: 0.46140, Accuracy: 77.91% | Test loss: 0.53556, Test acc: 72.38%
    Epoch: 140 | Loss: 0.46130, Accuracy: 77.91% | Test loss: 0.53554, Test acc: 72.38%
    Epoch: 150 | Loss: 0.46120, Accuracy: 77.93% | Test loss: 0.53553, Test acc: 72.38%
    Epoch: 160 | Loss: 0.46110, Accuracy: 77.95% | Test loss: 0.53551, Test acc: 72.33%
    Epoch: 170 | Loss: 0.46100, Accuracy: 77.95% | Test loss: 0.53550, Test acc: 72.33%
    Epoch: 180 | Loss: 0.46090, Accuracy: 77.97% | Test loss: 0.53548, Test acc: 72.33%
    Epoch: 190 | Loss: 0.46080, Accuracy: 77.97% | Test loss: 0.53547, Test acc: 72.38%
    Epoch: 200 | Loss: 0.46070, Accuracy: 78.00% | Test loss: 0.53545, Test acc: 72.33%
    Epoch: 210 | Loss: 0.46060, Accuracy: 77.99% | Test loss: 0.53544, Test acc: 72.28%
    Epoch: 220 | Loss: 0.46050, Accuracy: 77.98% | Test loss: 0.53543, Test acc: 72.33%
    Epoch: 230 | Loss: 0.46040, Accuracy: 78.00% | Test loss: 0.53541, Test acc: 72.28%
    Epoch: 240 | Loss: 0.46030, Accuracy: 78.02% | Test loss: 0.53540, Test acc: 72.28%
    Epoch: 250 | Loss: 0.46020, Accuracy: 78.02% | Test loss: 0.53539, Test acc: 72.23%
    Epoch: 260 | Loss: 0.46010, Accuracy: 78.03% | Test loss: 0.53538, Test acc: 72.23%
    Epoch: 270 | Loss: 0.46000, Accuracy: 78.04% | Test loss: 0.53537, Test acc: 72.23%
    Epoch: 280 | Loss: 0.45990, Accuracy: 78.04% | Test loss: 0.53536, Test acc: 72.23%
    Epoch: 290 | Loss: 0.45980, Accuracy: 78.06% | Test loss: 0.53535, Test acc: 72.23%
    Epoch: 300 | Loss: 0.45970, Accuracy: 78.04% | Test loss: 0.53534, Test acc: 72.23%
    Epoch: 310 | Loss: 0.45960, Accuracy: 78.04% | Test loss: 0.53533, Test acc: 72.23%
    Epoch: 320 | Loss: 0.45950, Accuracy: 78.04% | Test loss: 0.53532, Test acc: 72.17%
    Epoch: 330 | Loss: 0.45940, Accuracy: 78.04% | Test loss: 0.53531, Test acc: 72.17%
    Epoch: 340 | Loss: 0.45930, Accuracy: 78.04% | Test loss: 0.53530, Test acc: 72.17%
    Epoch: 350 | Loss: 0.45920, Accuracy: 78.06% | Test loss: 0.53529, Test acc: 72.17%
    Epoch: 360 | Loss: 0.45910, Accuracy: 78.08% | Test loss: 0.53528, Test acc: 72.17%
    Epoch: 370 | Loss: 0.45900, Accuracy: 78.07% | Test loss: 0.53527, Test acc: 72.17%
    Epoch: 380 | Loss: 0.45890, Accuracy: 78.08% | Test loss: 0.53527, Test acc: 72.17%
    Epoch: 390 | Loss: 0.45880, Accuracy: 78.11% | Test loss: 0.53526, Test acc: 72.17%
    Epoch: 400 | Loss: 0.45869, Accuracy: 78.12% | Test loss: 0.53525, Test acc: 72.17%
    Epoch: 410 | Loss: 0.45859, Accuracy: 78.12% | Test loss: 0.53524, Test acc: 72.23%
    Epoch: 420 | Loss: 0.45849, Accuracy: 78.13% | Test loss: 0.53523, Test acc: 72.23%
    Epoch: 430 | Loss: 0.45839, Accuracy: 78.13% | Test loss: 0.53522, Test acc: 72.23%
    Epoch: 440 | Loss: 0.45829, Accuracy: 78.12% | Test loss: 0.53521, Test acc: 72.23%
    Epoch: 450 | Loss: 0.45819, Accuracy: 78.12% | Test loss: 0.53519, Test acc: 72.23%
    Epoch: 460 | Loss: 0.45809, Accuracy: 78.12% | Test loss: 0.53518, Test acc: 72.23%
    Epoch: 470 | Loss: 0.45799, Accuracy: 78.11% | Test loss: 0.53517, Test acc: 72.17%
    Epoch: 480 | Loss: 0.45789, Accuracy: 78.11% | Test loss: 0.53516, Test acc: 72.17%
    Epoch: 490 | Loss: 0.45778, Accuracy: 78.08% | Test loss: 0.53515, Test acc: 72.17%
    Epoch: 500 | Loss: 0.45768, Accuracy: 78.08% | Test loss: 0.53513, Test acc: 72.17%
    Epoch: 510 | Loss: 0.45758, Accuracy: 78.09% | Test loss: 0.53512, Test acc: 72.17%
    Epoch: 520 | Loss: 0.45748, Accuracy: 78.09% | Test loss: 0.53511, Test acc: 72.17%
    Epoch: 530 | Loss: 0.45738, Accuracy: 78.17% | Test loss: 0.53510, Test acc: 72.23%
    Epoch: 540 | Loss: 0.45728, Accuracy: 78.19% | Test loss: 0.53509, Test acc: 72.23%
    Epoch: 550 | Loss: 0.45718, Accuracy: 78.20% | Test loss: 0.53508, Test acc: 72.28%
    Epoch: 560 | Loss: 0.45708, Accuracy: 78.21% | Test loss: 0.53507, Test acc: 72.28%
    Epoch: 570 | Loss: 0.45698, Accuracy: 78.19% | Test loss: 0.53505, Test acc: 72.23%
    Epoch: 580 | Loss: 0.45688, Accuracy: 78.19% | Test loss: 0.53504, Test acc: 72.23%
    Epoch: 590 | Loss: 0.45678, Accuracy: 78.17% | Test loss: 0.53503, Test acc: 72.23%
    Epoch: 600 | Loss: 0.45667, Accuracy: 78.19% | Test loss: 0.53502, Test acc: 72.28%
    Epoch: 610 | Loss: 0.45657, Accuracy: 78.19% | Test loss: 0.53501, Test acc: 72.33%
    Epoch: 620 | Loss: 0.45647, Accuracy: 78.21% | Test loss: 0.53500, Test acc: 72.33%
    Epoch: 630 | Loss: 0.45637, Accuracy: 78.22% | Test loss: 0.53500, Test acc: 72.33%
    Epoch: 640 | Loss: 0.45627, Accuracy: 78.24% | Test loss: 0.53499, Test acc: 72.33%
    Epoch: 650 | Loss: 0.45617, Accuracy: 78.26% | Test loss: 0.53498, Test acc: 72.28%
    Epoch: 660 | Loss: 0.45607, Accuracy: 78.28% | Test loss: 0.53497, Test acc: 72.28%
    Epoch: 670 | Loss: 0.45597, Accuracy: 78.30% | Test loss: 0.53497, Test acc: 72.28%
    Epoch: 680 | Loss: 0.45587, Accuracy: 78.30% | Test loss: 0.53496, Test acc: 72.28%
    Epoch: 690 | Loss: 0.45576, Accuracy: 78.33% | Test loss: 0.53495, Test acc: 72.28%
    Epoch: 700 | Loss: 0.45566, Accuracy: 78.34% | Test loss: 0.53494, Test acc: 72.33%
    Epoch: 710 | Loss: 0.45556, Accuracy: 78.35% | Test loss: 0.53493, Test acc: 72.33%
    Epoch: 720 | Loss: 0.45546, Accuracy: 78.35% | Test loss: 0.53492, Test acc: 72.38%
    Epoch: 730 | Loss: 0.45536, Accuracy: 78.35% | Test loss: 0.53492, Test acc: 72.38%
    Epoch: 740 | Loss: 0.45526, Accuracy: 78.38% | Test loss: 0.53491, Test acc: 72.38%
    Epoch: 750 | Loss: 0.45516, Accuracy: 78.35% | Test loss: 0.53490, Test acc: 72.33%
    Epoch: 760 | Loss: 0.45506, Accuracy: 78.33% | Test loss: 0.53489, Test acc: 72.28%
    Epoch: 770 | Loss: 0.45496, Accuracy: 78.33% | Test loss: 0.53488, Test acc: 72.28%
    Epoch: 780 | Loss: 0.45485, Accuracy: 78.35% | Test loss: 0.53488, Test acc: 72.28%
    Epoch: 790 | Loss: 0.45475, Accuracy: 78.37% | Test loss: 0.53487, Test acc: 72.28%
    Epoch: 800 | Loss: 0.45465, Accuracy: 78.37% | Test loss: 0.53486, Test acc: 72.28%
    Epoch: 810 | Loss: 0.45455, Accuracy: 78.37% | Test loss: 0.53485, Test acc: 72.28%
    Epoch: 820 | Loss: 0.45445, Accuracy: 78.39% | Test loss: 0.53484, Test acc: 72.33%
    Epoch: 830 | Loss: 0.45435, Accuracy: 78.38% | Test loss: 0.53483, Test acc: 72.28%
    Epoch: 840 | Loss: 0.45425, Accuracy: 78.37% | Test loss: 0.53482, Test acc: 72.28%
    Epoch: 850 | Loss: 0.45414, Accuracy: 78.39% | Test loss: 0.53481, Test acc: 72.28%
    Epoch: 860 | Loss: 0.45404, Accuracy: 78.40% | Test loss: 0.53480, Test acc: 72.28%
    Epoch: 870 | Loss: 0.45394, Accuracy: 78.42% | Test loss: 0.53479, Test acc: 72.28%
    Epoch: 880 | Loss: 0.45384, Accuracy: 78.44% | Test loss: 0.53478, Test acc: 72.28%
    Epoch: 890 | Loss: 0.45374, Accuracy: 78.46% | Test loss: 0.53476, Test acc: 72.33%
    Epoch: 900 | Loss: 0.45364, Accuracy: 78.47% | Test loss: 0.53475, Test acc: 72.33%
    Epoch: 910 | Loss: 0.45353, Accuracy: 78.48% | Test loss: 0.53474, Test acc: 72.33%
    Epoch: 920 | Loss: 0.45343, Accuracy: 78.49% | Test loss: 0.53473, Test acc: 72.33%
    Epoch: 930 | Loss: 0.45333, Accuracy: 78.52% | Test loss: 0.53472, Test acc: 72.38%
    Epoch: 940 | Loss: 0.45323, Accuracy: 78.52% | Test loss: 0.53471, Test acc: 72.38%
    Epoch: 950 | Loss: 0.45313, Accuracy: 78.51% | Test loss: 0.53470, Test acc: 72.38%
    Epoch: 960 | Loss: 0.45303, Accuracy: 78.52% | Test loss: 0.53469, Test acc: 72.33%
    Epoch: 970 | Loss: 0.45293, Accuracy: 78.51% | Test loss: 0.53468, Test acc: 72.33%
    Epoch: 980 | Loss: 0.45282, Accuracy: 78.51% | Test loss: 0.53467, Test acc: 72.33%
    Epoch: 990 | Loss: 0.45272, Accuracy: 78.53% | Test loss: 0.53466, Test acc: 72.33%
    Epoch: 1000 | Loss: 0.45262, Accuracy: 78.53% | Test loss: 0.53465, Test acc: 72.38%
    Epoch: 1010 | Loss: 0.45252, Accuracy: 78.53% | Test loss: 0.53464, Test acc: 72.38%
    Epoch: 1020 | Loss: 0.45242, Accuracy: 78.53% | Test loss: 0.53463, Test acc: 72.38%
    Epoch: 1030 | Loss: 0.45232, Accuracy: 78.53% | Test loss: 0.53462, Test acc: 72.43%
    Epoch: 1040 | Loss: 0.45222, Accuracy: 78.55% | Test loss: 0.53462, Test acc: 72.43%
    Epoch: 1050 | Loss: 0.45212, Accuracy: 78.55% | Test loss: 0.53461, Test acc: 72.43%
    Epoch: 1060 | Loss: 0.45202, Accuracy: 78.56% | Test loss: 0.53460, Test acc: 72.43%
    Epoch: 1070 | Loss: 0.45192, Accuracy: 78.56% | Test loss: 0.53459, Test acc: 72.43%
    Epoch: 1080 | Loss: 0.45182, Accuracy: 78.56% | Test loss: 0.53458, Test acc: 72.43%
    Epoch: 1090 | Loss: 0.45172, Accuracy: 78.55% | Test loss: 0.53458, Test acc: 72.48%
    Epoch: 1100 | Loss: 0.45161, Accuracy: 78.55% | Test loss: 0.53457, Test acc: 72.48%
    Epoch: 1110 | Loss: 0.45151, Accuracy: 78.56% | Test loss: 0.53456, Test acc: 72.48%
    Epoch: 1120 | Loss: 0.45141, Accuracy: 78.57% | Test loss: 0.53455, Test acc: 72.48%
    Epoch: 1130 | Loss: 0.45131, Accuracy: 78.59% | Test loss: 0.53454, Test acc: 72.48%
    Epoch: 1140 | Loss: 0.45121, Accuracy: 78.60% | Test loss: 0.53453, Test acc: 72.48%
    Epoch: 1150 | Loss: 0.45111, Accuracy: 78.60% | Test loss: 0.53452, Test acc: 72.48%
    Epoch: 1160 | Loss: 0.45101, Accuracy: 78.60% | Test loss: 0.53451, Test acc: 72.48%
    Epoch: 1170 | Loss: 0.45091, Accuracy: 78.61% | Test loss: 0.53450, Test acc: 72.48%
    Epoch: 1180 | Loss: 0.45081, Accuracy: 78.60% | Test loss: 0.53450, Test acc: 72.48%
    Epoch: 1190 | Loss: 0.45071, Accuracy: 78.59% | Test loss: 0.53449, Test acc: 72.48%
    Epoch: 1200 | Loss: 0.45061, Accuracy: 78.61% | Test loss: 0.53448, Test acc: 72.48%
    Epoch: 1210 | Loss: 0.45051, Accuracy: 78.60% | Test loss: 0.53447, Test acc: 72.48%
    Epoch: 1220 | Loss: 0.45041, Accuracy: 78.60% | Test loss: 0.53446, Test acc: 72.48%
    Epoch: 1230 | Loss: 0.45031, Accuracy: 78.60% | Test loss: 0.53445, Test acc: 72.48%
    Epoch: 1240 | Loss: 0.45021, Accuracy: 78.61% | Test loss: 0.53444, Test acc: 72.48%
    Epoch: 1250 | Loss: 0.45011, Accuracy: 78.61% | Test loss: 0.53443, Test acc: 72.48%
    Epoch: 1260 | Loss: 0.45001, Accuracy: 78.61% | Test loss: 0.53442, Test acc: 72.48%
    Epoch: 1270 | Loss: 0.44991, Accuracy: 78.61% | Test loss: 0.53441, Test acc: 72.48%
    Epoch: 1280 | Loss: 0.44981, Accuracy: 78.64% | Test loss: 0.53440, Test acc: 72.48%
    Epoch: 1290 | Loss: 0.44971, Accuracy: 78.64% | Test loss: 0.53439, Test acc: 72.48%
    Epoch: 1300 | Loss: 0.44961, Accuracy: 78.64% | Test loss: 0.53438, Test acc: 72.48%
    Epoch: 1310 | Loss: 0.44951, Accuracy: 78.64% | Test loss: 0.53437, Test acc: 72.48%
    Epoch: 1320 | Loss: 0.44940, Accuracy: 78.65% | Test loss: 0.53437, Test acc: 72.48%
    Epoch: 1330 | Loss: 0.44930, Accuracy: 78.66% | Test loss: 0.53435, Test acc: 72.53%
    Epoch: 1340 | Loss: 0.44920, Accuracy: 78.66% | Test loss: 0.53434, Test acc: 72.53%
    Epoch: 1350 | Loss: 0.44910, Accuracy: 78.68% | Test loss: 0.53434, Test acc: 72.53%
    Epoch: 1360 | Loss: 0.44900, Accuracy: 78.69% | Test loss: 0.53433, Test acc: 72.53%
    Epoch: 1370 | Loss: 0.44890, Accuracy: 78.70% | Test loss: 0.53432, Test acc: 72.53%
    Epoch: 1380 | Loss: 0.44880, Accuracy: 78.71% | Test loss: 0.53431, Test acc: 72.53%
    Epoch: 1390 | Loss: 0.44870, Accuracy: 78.73% | Test loss: 0.53430, Test acc: 72.53%
    Epoch: 1400 | Loss: 0.44860, Accuracy: 78.74% | Test loss: 0.53430, Test acc: 72.48%
    Epoch: 1410 | Loss: 0.44850, Accuracy: 78.77% | Test loss: 0.53429, Test acc: 72.48%
    Epoch: 1420 | Loss: 0.44840, Accuracy: 78.77% | Test loss: 0.53428, Test acc: 72.48%
    Epoch: 1430 | Loss: 0.44830, Accuracy: 78.77% | Test loss: 0.53428, Test acc: 72.48%
    Epoch: 1440 | Loss: 0.44820, Accuracy: 78.78% | Test loss: 0.53427, Test acc: 72.48%
    Epoch: 1450 | Loss: 0.44810, Accuracy: 78.79% | Test loss: 0.53427, Test acc: 72.53%
    Epoch: 1460 | Loss: 0.44800, Accuracy: 78.79% | Test loss: 0.53426, Test acc: 72.59%
    Epoch: 1470 | Loss: 0.44790, Accuracy: 78.79% | Test loss: 0.53425, Test acc: 72.59%
    Epoch: 1480 | Loss: 0.44780, Accuracy: 78.79% | Test loss: 0.53424, Test acc: 72.59%
    Epoch: 1490 | Loss: 0.44770, Accuracy: 78.82% | Test loss: 0.53424, Test acc: 72.59%
    Epoch: 1500 | Loss: 0.44760, Accuracy: 78.86% | Test loss: 0.53423, Test acc: 72.64%
    Epoch: 1510 | Loss: 0.44750, Accuracy: 78.84% | Test loss: 0.53422, Test acc: 72.64%
    Epoch: 1520 | Loss: 0.44740, Accuracy: 78.86% | Test loss: 0.53422, Test acc: 72.64%
    Epoch: 1530 | Loss: 0.44730, Accuracy: 78.87% | Test loss: 0.53421, Test acc: 72.64%
    Epoch: 1540 | Loss: 0.44720, Accuracy: 78.88% | Test loss: 0.53420, Test acc: 72.64%
    Epoch: 1550 | Loss: 0.44710, Accuracy: 78.88% | Test loss: 0.53419, Test acc: 72.64%
    Epoch: 1560 | Loss: 0.44700, Accuracy: 78.90% | Test loss: 0.53419, Test acc: 72.64%
    Epoch: 1570 | Loss: 0.44690, Accuracy: 78.92% | Test loss: 0.53418, Test acc: 72.69%
    Epoch: 1580 | Loss: 0.44679, Accuracy: 78.93% | Test loss: 0.53417, Test acc: 72.69%
    Epoch: 1590 | Loss: 0.44669, Accuracy: 78.99% | Test loss: 0.53416, Test acc: 72.59%
    Epoch: 1600 | Loss: 0.44659, Accuracy: 79.01% | Test loss: 0.53415, Test acc: 72.59%
    Epoch: 1610 | Loss: 0.44649, Accuracy: 79.01% | Test loss: 0.53415, Test acc: 72.59%
    Epoch: 1620 | Loss: 0.44639, Accuracy: 79.00% | Test loss: 0.53414, Test acc: 72.59%
    Epoch: 1630 | Loss: 0.44629, Accuracy: 79.01% | Test loss: 0.53413, Test acc: 72.53%
    Epoch: 1640 | Loss: 0.44619, Accuracy: 79.02% | Test loss: 0.53412, Test acc: 72.53%
    Epoch: 1650 | Loss: 0.44609, Accuracy: 79.01% | Test loss: 0.53412, Test acc: 72.53%
    Epoch: 1660 | Loss: 0.44598, Accuracy: 79.01% | Test loss: 0.53411, Test acc: 72.53%
    Epoch: 1670 | Loss: 0.44588, Accuracy: 79.02% | Test loss: 0.53410, Test acc: 72.48%
    Epoch: 1680 | Loss: 0.44578, Accuracy: 79.04% | Test loss: 0.53410, Test acc: 72.48%
    Epoch: 1690 | Loss: 0.44568, Accuracy: 79.04% | Test loss: 0.53409, Test acc: 72.48%
    Epoch: 1700 | Loss: 0.44558, Accuracy: 79.06% | Test loss: 0.53409, Test acc: 72.48%
    Epoch: 1710 | Loss: 0.44548, Accuracy: 79.11% | Test loss: 0.53408, Test acc: 72.48%
    Epoch: 1720 | Loss: 0.44537, Accuracy: 79.11% | Test loss: 0.53407, Test acc: 72.43%
    Epoch: 1730 | Loss: 0.44527, Accuracy: 79.11% | Test loss: 0.53407, Test acc: 72.48%
    Epoch: 1740 | Loss: 0.44517, Accuracy: 79.13% | Test loss: 0.53407, Test acc: 72.48%
    Epoch: 1750 | Loss: 0.44507, Accuracy: 79.13% | Test loss: 0.53406, Test acc: 72.59%
    Epoch: 1760 | Loss: 0.44496, Accuracy: 79.13% | Test loss: 0.53406, Test acc: 72.59%
    Epoch: 1770 | Loss: 0.44486, Accuracy: 79.14% | Test loss: 0.53405, Test acc: 72.64%
    Epoch: 1780 | Loss: 0.44476, Accuracy: 79.18% | Test loss: 0.53405, Test acc: 72.64%
    Epoch: 1790 | Loss: 0.44466, Accuracy: 79.18% | Test loss: 0.53405, Test acc: 72.64%
    Epoch: 1800 | Loss: 0.44456, Accuracy: 79.18% | Test loss: 0.53404, Test acc: 72.64%
    Epoch: 1810 | Loss: 0.44445, Accuracy: 79.19% | Test loss: 0.53404, Test acc: 72.64%
    Epoch: 1820 | Loss: 0.44435, Accuracy: 79.20% | Test loss: 0.53403, Test acc: 72.69%
    Epoch: 1830 | Loss: 0.44425, Accuracy: 79.22% | Test loss: 0.53402, Test acc: 72.74%
    Epoch: 1840 | Loss: 0.44415, Accuracy: 79.20% | Test loss: 0.53402, Test acc: 72.74%
    Epoch: 1850 | Loss: 0.44404, Accuracy: 79.19% | Test loss: 0.53401, Test acc: 72.79%
    Epoch: 1860 | Loss: 0.44394, Accuracy: 79.19% | Test loss: 0.53401, Test acc: 72.79%
    Epoch: 1870 | Loss: 0.44384, Accuracy: 79.19% | Test loss: 0.53400, Test acc: 72.79%
    Epoch: 1880 | Loss: 0.44374, Accuracy: 79.26% | Test loss: 0.53400, Test acc: 72.79%
    Epoch: 1890 | Loss: 0.44363, Accuracy: 79.26% | Test loss: 0.53399, Test acc: 72.79%
    Epoch: 1900 | Loss: 0.44353, Accuracy: 79.26% | Test loss: 0.53399, Test acc: 72.79%
    Epoch: 1910 | Loss: 0.44343, Accuracy: 79.27% | Test loss: 0.53398, Test acc: 72.79%
    Epoch: 1920 | Loss: 0.44332, Accuracy: 79.27% | Test loss: 0.53398, Test acc: 72.79%
    Epoch: 1930 | Loss: 0.44322, Accuracy: 79.27% | Test loss: 0.53397, Test acc: 72.79%
    Epoch: 1940 | Loss: 0.44312, Accuracy: 79.27% | Test loss: 0.53397, Test acc: 72.79%
    Epoch: 1950 | Loss: 0.44302, Accuracy: 79.27% | Test loss: 0.53396, Test acc: 72.79%
    Epoch: 1960 | Loss: 0.44291, Accuracy: 79.28% | Test loss: 0.53396, Test acc: 72.79%
    Epoch: 1970 | Loss: 0.44281, Accuracy: 79.28% | Test loss: 0.53395, Test acc: 72.79%
    Epoch: 1980 | Loss: 0.44271, Accuracy: 79.28% | Test loss: 0.53395, Test acc: 72.79%
    Epoch: 1990 | Loss: 0.44260, Accuracy: 79.30% | Test loss: 0.53394, Test acc: 72.79%
    Epoch: 2000 | Loss: 0.44250, Accuracy: 79.32% | Test loss: 0.53394, Test acc: 72.79%
    Epoch: 2010 | Loss: 0.44240, Accuracy: 79.33% | Test loss: 0.53393, Test acc: 72.79%
    Epoch: 2020 | Loss: 0.44230, Accuracy: 79.36% | Test loss: 0.53393, Test acc: 72.79%
    Epoch: 2030 | Loss: 0.44219, Accuracy: 79.37% | Test loss: 0.53392, Test acc: 72.79%
    Epoch: 2040 | Loss: 0.44209, Accuracy: 79.36% | Test loss: 0.53392, Test acc: 72.79%
    Epoch: 2050 | Loss: 0.44199, Accuracy: 79.37% | Test loss: 0.53391, Test acc: 72.79%
    Epoch: 2060 | Loss: 0.44189, Accuracy: 79.37% | Test loss: 0.53391, Test acc: 72.74%
    Epoch: 2070 | Loss: 0.44179, Accuracy: 79.39% | Test loss: 0.53391, Test acc: 72.74%
    Epoch: 2080 | Loss: 0.44168, Accuracy: 79.40% | Test loss: 0.53390, Test acc: 72.74%
    Epoch: 2090 | Loss: 0.44158, Accuracy: 79.44% | Test loss: 0.53390, Test acc: 72.74%
    Epoch: 2100 | Loss: 0.44148, Accuracy: 79.45% | Test loss: 0.53390, Test acc: 72.74%
    Epoch: 2110 | Loss: 0.44138, Accuracy: 79.45% | Test loss: 0.53389, Test acc: 72.74%
    Epoch: 2120 | Loss: 0.44127, Accuracy: 79.49% | Test loss: 0.53389, Test acc: 72.69%
    Epoch: 2130 | Loss: 0.44117, Accuracy: 79.49% | Test loss: 0.53389, Test acc: 72.69%
    Epoch: 2140 | Loss: 0.44107, Accuracy: 79.50% | Test loss: 0.53389, Test acc: 72.69%
    Epoch: 2150 | Loss: 0.44097, Accuracy: 79.51% | Test loss: 0.53389, Test acc: 72.69%
    Epoch: 2160 | Loss: 0.44087, Accuracy: 79.51% | Test loss: 0.53389, Test acc: 72.69%
    Epoch: 2170 | Loss: 0.44076, Accuracy: 79.53% | Test loss: 0.53389, Test acc: 72.69%
    Epoch: 2180 | Loss: 0.44066, Accuracy: 79.53% | Test loss: 0.53389, Test acc: 72.74%
    Epoch: 2190 | Loss: 0.44056, Accuracy: 79.53% | Test loss: 0.53389, Test acc: 72.74%
    Epoch: 2200 | Loss: 0.44046, Accuracy: 79.50% | Test loss: 0.53389, Test acc: 72.69%
    Epoch: 2210 | Loss: 0.44035, Accuracy: 79.53% | Test loss: 0.53388, Test acc: 72.74%
    Epoch: 2220 | Loss: 0.44025, Accuracy: 79.53% | Test loss: 0.53388, Test acc: 72.74%
    Epoch: 2230 | Loss: 0.44015, Accuracy: 79.53% | Test loss: 0.53388, Test acc: 72.74%
    Epoch: 2240 | Loss: 0.44005, Accuracy: 79.53% | Test loss: 0.53387, Test acc: 72.74%
    Epoch: 2250 | Loss: 0.43994, Accuracy: 79.57% | Test loss: 0.53387, Test acc: 72.74%
    Epoch: 2260 | Loss: 0.43984, Accuracy: 79.58% | Test loss: 0.53387, Test acc: 72.74%
    Epoch: 2270 | Loss: 0.43974, Accuracy: 79.58% | Test loss: 0.53387, Test acc: 72.79%
    Epoch: 2280 | Loss: 0.43964, Accuracy: 79.61% | Test loss: 0.53387, Test acc: 72.84%
    Epoch: 2290 | Loss: 0.43953, Accuracy: 79.59% | Test loss: 0.53387, Test acc: 72.84%
    Epoch: 2300 | Loss: 0.43943, Accuracy: 79.58% | Test loss: 0.53387, Test acc: 72.84%
    Epoch: 2310 | Loss: 0.43933, Accuracy: 79.59% | Test loss: 0.53387, Test acc: 72.79%
    Epoch: 2320 | Loss: 0.43923, Accuracy: 79.59% | Test loss: 0.53387, Test acc: 72.79%
    Epoch: 2330 | Loss: 0.43912, Accuracy: 79.59% | Test loss: 0.53387, Test acc: 72.79%
    Epoch: 2340 | Loss: 0.43902, Accuracy: 79.59% | Test loss: 0.53387, Test acc: 72.79%
    Epoch: 2350 | Loss: 0.43892, Accuracy: 79.59% | Test loss: 0.53387, Test acc: 72.84%
    Epoch: 2360 | Loss: 0.43881, Accuracy: 79.59% | Test loss: 0.53387, Test acc: 72.84%
    Epoch: 2370 | Loss: 0.43871, Accuracy: 79.61% | Test loss: 0.53387, Test acc: 72.84%
    Epoch: 2380 | Loss: 0.43861, Accuracy: 79.59% | Test loss: 0.53387, Test acc: 72.79%
    Epoch: 2390 | Loss: 0.43850, Accuracy: 79.59% | Test loss: 0.53387, Test acc: 72.79%
    Epoch: 2400 | Loss: 0.43840, Accuracy: 79.59% | Test loss: 0.53387, Test acc: 72.79%
    Epoch: 2410 | Loss: 0.43830, Accuracy: 79.61% | Test loss: 0.53387, Test acc: 72.69%
    Epoch: 2420 | Loss: 0.43819, Accuracy: 79.61% | Test loss: 0.53388, Test acc: 72.69%
    Epoch: 2430 | Loss: 0.43809, Accuracy: 79.61% | Test loss: 0.53388, Test acc: 72.69%
    Epoch: 2440 | Loss: 0.43799, Accuracy: 79.59% | Test loss: 0.53388, Test acc: 72.69%
    Epoch: 2450 | Loss: 0.43788, Accuracy: 79.59% | Test loss: 0.53388, Test acc: 72.69%
    Epoch: 2460 | Loss: 0.43778, Accuracy: 79.62% | Test loss: 0.53389, Test acc: 72.69%
    Epoch: 2470 | Loss: 0.43767, Accuracy: 79.62% | Test loss: 0.53389, Test acc: 72.69%
    Epoch: 2480 | Loss: 0.43757, Accuracy: 79.63% | Test loss: 0.53389, Test acc: 72.64%
    Epoch: 2490 | Loss: 0.43747, Accuracy: 79.63% | Test loss: 0.53390, Test acc: 72.69%
    Epoch: 2500 | Loss: 0.43736, Accuracy: 79.66% | Test loss: 0.53390, Test acc: 72.69%
    Epoch: 2510 | Loss: 0.43726, Accuracy: 79.67% | Test loss: 0.53391, Test acc: 72.69%
    Epoch: 2520 | Loss: 0.43716, Accuracy: 79.66% | Test loss: 0.53391, Test acc: 72.74%
    Epoch: 2530 | Loss: 0.43705, Accuracy: 79.66% | Test loss: 0.53392, Test acc: 72.74%
    Epoch: 2540 | Loss: 0.43695, Accuracy: 79.67% | Test loss: 0.53392, Test acc: 72.74%
    Epoch: 2550 | Loss: 0.43685, Accuracy: 79.67% | Test loss: 0.53393, Test acc: 72.79%
    Epoch: 2560 | Loss: 0.43674, Accuracy: 79.68% | Test loss: 0.53393, Test acc: 72.79%
    Epoch: 2570 | Loss: 0.43664, Accuracy: 79.71% | Test loss: 0.53393, Test acc: 72.79%
    Epoch: 2580 | Loss: 0.43654, Accuracy: 79.71% | Test loss: 0.53394, Test acc: 72.79%
    Epoch: 2590 | Loss: 0.43643, Accuracy: 79.71% | Test loss: 0.53394, Test acc: 72.79%
    Epoch: 2600 | Loss: 0.43633, Accuracy: 79.71% | Test loss: 0.53395, Test acc: 72.79%
    Epoch: 2610 | Loss: 0.43622, Accuracy: 79.72% | Test loss: 0.53395, Test acc: 72.79%
    Epoch: 2620 | Loss: 0.43612, Accuracy: 79.70% | Test loss: 0.53396, Test acc: 72.79%
    Epoch: 2630 | Loss: 0.43602, Accuracy: 79.71% | Test loss: 0.53396, Test acc: 72.79%
    Epoch: 2640 | Loss: 0.43591, Accuracy: 79.72% | Test loss: 0.53397, Test acc: 72.84%
    Epoch: 2650 | Loss: 0.43581, Accuracy: 79.73% | Test loss: 0.53397, Test acc: 72.84%
    Epoch: 2660 | Loss: 0.43571, Accuracy: 79.73% | Test loss: 0.53398, Test acc: 72.84%
    Epoch: 2670 | Loss: 0.43560, Accuracy: 79.73% | Test loss: 0.53398, Test acc: 72.84%
    Epoch: 2680 | Loss: 0.43550, Accuracy: 79.73% | Test loss: 0.53399, Test acc: 72.84%
    Epoch: 2690 | Loss: 0.43540, Accuracy: 79.73% | Test loss: 0.53400, Test acc: 72.90%
    Epoch: 2700 | Loss: 0.43529, Accuracy: 79.73% | Test loss: 0.53401, Test acc: 72.90%
    Epoch: 2710 | Loss: 0.43519, Accuracy: 79.73% | Test loss: 0.53402, Test acc: 72.90%
    Epoch: 2720 | Loss: 0.43508, Accuracy: 79.76% | Test loss: 0.53402, Test acc: 72.90%
    Epoch: 2730 | Loss: 0.43498, Accuracy: 79.77% | Test loss: 0.53403, Test acc: 72.90%
    Epoch: 2740 | Loss: 0.43488, Accuracy: 79.81% | Test loss: 0.53404, Test acc: 72.90%
    Epoch: 2750 | Loss: 0.43477, Accuracy: 79.79% | Test loss: 0.53405, Test acc: 72.84%
    Epoch: 2760 | Loss: 0.43467, Accuracy: 79.80% | Test loss: 0.53406, Test acc: 72.84%
    Epoch: 2770 | Loss: 0.43456, Accuracy: 79.79% | Test loss: 0.53406, Test acc: 72.84%
    Epoch: 2780 | Loss: 0.43446, Accuracy: 79.81% | Test loss: 0.53407, Test acc: 72.90%
    Epoch: 2790 | Loss: 0.43435, Accuracy: 79.82% | Test loss: 0.53407, Test acc: 72.90%
    Epoch: 2800 | Loss: 0.43425, Accuracy: 79.84% | Test loss: 0.53407, Test acc: 72.95%
    Epoch: 2810 | Loss: 0.43414, Accuracy: 79.84% | Test loss: 0.53407, Test acc: 73.00%
    Epoch: 2820 | Loss: 0.43404, Accuracy: 79.86% | Test loss: 0.53408, Test acc: 72.95%
    Epoch: 2830 | Loss: 0.43393, Accuracy: 79.89% | Test loss: 0.53408, Test acc: 72.95%
    Epoch: 2840 | Loss: 0.43383, Accuracy: 79.90% | Test loss: 0.53408, Test acc: 72.90%
    Epoch: 2850 | Loss: 0.43372, Accuracy: 79.93% | Test loss: 0.53408, Test acc: 72.90%
    Epoch: 2860 | Loss: 0.43362, Accuracy: 79.93% | Test loss: 0.53408, Test acc: 72.90%
    Epoch: 2870 | Loss: 0.43352, Accuracy: 79.95% | Test loss: 0.53409, Test acc: 72.90%
    Epoch: 2880 | Loss: 0.43341, Accuracy: 79.95% | Test loss: 0.53409, Test acc: 72.90%
    Epoch: 2890 | Loss: 0.43331, Accuracy: 79.95% | Test loss: 0.53410, Test acc: 72.90%
    Epoch: 2900 | Loss: 0.43320, Accuracy: 79.94% | Test loss: 0.53410, Test acc: 72.90%
    Epoch: 2910 | Loss: 0.43310, Accuracy: 79.94% | Test loss: 0.53410, Test acc: 72.84%
    Epoch: 2920 | Loss: 0.43299, Accuracy: 79.95% | Test loss: 0.53411, Test acc: 72.84%
    Epoch: 2930 | Loss: 0.43289, Accuracy: 79.95% | Test loss: 0.53411, Test acc: 72.84%
    Epoch: 2940 | Loss: 0.43278, Accuracy: 79.98% | Test loss: 0.53411, Test acc: 72.84%
    Epoch: 2950 | Loss: 0.43268, Accuracy: 79.97% | Test loss: 0.53412, Test acc: 72.84%
    Epoch: 2960 | Loss: 0.43257, Accuracy: 79.98% | Test loss: 0.53412, Test acc: 72.84%
    Epoch: 2970 | Loss: 0.43247, Accuracy: 79.98% | Test loss: 0.53412, Test acc: 72.84%
    Epoch: 2980 | Loss: 0.43236, Accuracy: 80.01% | Test loss: 0.53412, Test acc: 72.84%
    Epoch: 2990 | Loss: 0.43226, Accuracy: 80.02% | Test loss: 0.53413, Test acc: 72.84%
    Epoch: 3000 | Loss: 0.43215, Accuracy: 79.99% | Test loss: 0.53413, Test acc: 72.84%
    Epoch: 3010 | Loss: 0.43205, Accuracy: 79.98% | Test loss: 0.53413, Test acc: 72.84%
    Epoch: 3020 | Loss: 0.43194, Accuracy: 79.99% | Test loss: 0.53414, Test acc: 72.84%
    Epoch: 3030 | Loss: 0.43184, Accuracy: 79.98% | Test loss: 0.53414, Test acc: 72.84%
    Epoch: 3040 | Loss: 0.43173, Accuracy: 79.98% | Test loss: 0.53415, Test acc: 72.84%
    Epoch: 3050 | Loss: 0.43163, Accuracy: 79.98% | Test loss: 0.53415, Test acc: 72.84%
    Epoch: 3060 | Loss: 0.43152, Accuracy: 79.95% | Test loss: 0.53415, Test acc: 72.84%
    Epoch: 3070 | Loss: 0.43142, Accuracy: 79.97% | Test loss: 0.53416, Test acc: 72.84%
    Epoch: 3080 | Loss: 0.43131, Accuracy: 79.98% | Test loss: 0.53416, Test acc: 72.84%
    Epoch: 3090 | Loss: 0.43121, Accuracy: 79.98% | Test loss: 0.53417, Test acc: 72.90%
    Epoch: 3100 | Loss: 0.43110, Accuracy: 79.98% | Test loss: 0.53418, Test acc: 72.90%
    Epoch: 3110 | Loss: 0.43099, Accuracy: 79.99% | Test loss: 0.53418, Test acc: 72.90%
    Epoch: 3120 | Loss: 0.43089, Accuracy: 80.02% | Test loss: 0.53419, Test acc: 72.90%
    Epoch: 3130 | Loss: 0.43078, Accuracy: 80.02% | Test loss: 0.53420, Test acc: 72.90%
    Epoch: 3140 | Loss: 0.43068, Accuracy: 80.03% | Test loss: 0.53420, Test acc: 72.90%
    Epoch: 3150 | Loss: 0.43057, Accuracy: 80.04% | Test loss: 0.53421, Test acc: 72.90%
    Epoch: 3160 | Loss: 0.43047, Accuracy: 80.04% | Test loss: 0.53421, Test acc: 72.90%
    Epoch: 3170 | Loss: 0.43036, Accuracy: 80.06% | Test loss: 0.53422, Test acc: 72.90%
    Epoch: 3180 | Loss: 0.43025, Accuracy: 80.08% | Test loss: 0.53422, Test acc: 72.90%
    Epoch: 3190 | Loss: 0.43015, Accuracy: 80.10% | Test loss: 0.53423, Test acc: 72.90%
    Epoch: 3200 | Loss: 0.43004, Accuracy: 80.08% | Test loss: 0.53424, Test acc: 72.95%
    Epoch: 3210 | Loss: 0.42994, Accuracy: 80.10% | Test loss: 0.53424, Test acc: 72.95%
    Epoch: 3220 | Loss: 0.42983, Accuracy: 80.10% | Test loss: 0.53425, Test acc: 73.00%
    Epoch: 3230 | Loss: 0.42972, Accuracy: 80.10% | Test loss: 0.53426, Test acc: 73.00%
    Epoch: 3240 | Loss: 0.42962, Accuracy: 80.10% | Test loss: 0.53426, Test acc: 73.05%
    Epoch: 3250 | Loss: 0.42951, Accuracy: 80.08% | Test loss: 0.53427, Test acc: 73.05%
    Epoch: 3260 | Loss: 0.42941, Accuracy: 80.10% | Test loss: 0.53428, Test acc: 73.05%
    Epoch: 3270 | Loss: 0.42930, Accuracy: 80.12% | Test loss: 0.53428, Test acc: 73.05%
    Epoch: 3280 | Loss: 0.42919, Accuracy: 80.12% | Test loss: 0.53429, Test acc: 73.05%
    Epoch: 3290 | Loss: 0.42909, Accuracy: 80.13% | Test loss: 0.53429, Test acc: 73.05%
    Epoch: 3300 | Loss: 0.42898, Accuracy: 80.15% | Test loss: 0.53430, Test acc: 73.05%
    Epoch: 3310 | Loss: 0.42888, Accuracy: 80.15% | Test loss: 0.53431, Test acc: 73.05%
    Epoch: 3320 | Loss: 0.42877, Accuracy: 80.16% | Test loss: 0.53432, Test acc: 73.05%
    Epoch: 3330 | Loss: 0.42866, Accuracy: 80.15% | Test loss: 0.53433, Test acc: 73.05%
    Epoch: 3340 | Loss: 0.42856, Accuracy: 80.15% | Test loss: 0.53433, Test acc: 73.05%
    Epoch: 3350 | Loss: 0.42845, Accuracy: 80.16% | Test loss: 0.53434, Test acc: 73.05%
    Epoch: 3360 | Loss: 0.42834, Accuracy: 80.15% | Test loss: 0.53435, Test acc: 73.05%
    Epoch: 3370 | Loss: 0.42824, Accuracy: 80.15% | Test loss: 0.53436, Test acc: 73.00%
    Epoch: 3380 | Loss: 0.42813, Accuracy: 80.17% | Test loss: 0.53436, Test acc: 73.00%
    Epoch: 3390 | Loss: 0.42802, Accuracy: 80.19% | Test loss: 0.53437, Test acc: 73.00%
    Epoch: 3400 | Loss: 0.42791, Accuracy: 80.20% | Test loss: 0.53438, Test acc: 73.00%
    Epoch: 3410 | Loss: 0.42781, Accuracy: 80.20% | Test loss: 0.53438, Test acc: 73.00%
    Epoch: 3420 | Loss: 0.42770, Accuracy: 80.21% | Test loss: 0.53439, Test acc: 73.10%
    Epoch: 3430 | Loss: 0.42759, Accuracy: 80.22% | Test loss: 0.53439, Test acc: 73.10%
    Epoch: 3440 | Loss: 0.42749, Accuracy: 80.22% | Test loss: 0.53440, Test acc: 73.05%
    Epoch: 3450 | Loss: 0.42738, Accuracy: 80.25% | Test loss: 0.53440, Test acc: 73.05%
    Epoch: 3460 | Loss: 0.42727, Accuracy: 80.25% | Test loss: 0.53440, Test acc: 73.05%
    Epoch: 3470 | Loss: 0.42716, Accuracy: 80.25% | Test loss: 0.53441, Test acc: 73.10%
    Epoch: 3480 | Loss: 0.42705, Accuracy: 80.26% | Test loss: 0.53441, Test acc: 73.10%
    Epoch: 3490 | Loss: 0.42694, Accuracy: 80.29% | Test loss: 0.53442, Test acc: 73.10%
    Epoch: 3500 | Loss: 0.42684, Accuracy: 80.30% | Test loss: 0.53442, Test acc: 73.05%
    Epoch: 3510 | Loss: 0.42673, Accuracy: 80.29% | Test loss: 0.53443, Test acc: 73.05%
    Epoch: 3520 | Loss: 0.42662, Accuracy: 80.30% | Test loss: 0.53443, Test acc: 73.00%
    Epoch: 3530 | Loss: 0.42651, Accuracy: 80.31% | Test loss: 0.53444, Test acc: 73.00%
    Epoch: 3540 | Loss: 0.42640, Accuracy: 80.31% | Test loss: 0.53445, Test acc: 73.00%
    Epoch: 3550 | Loss: 0.42629, Accuracy: 80.31% | Test loss: 0.53445, Test acc: 73.05%
    Epoch: 3560 | Loss: 0.42618, Accuracy: 80.33% | Test loss: 0.53446, Test acc: 73.10%
    Epoch: 3570 | Loss: 0.42608, Accuracy: 80.33% | Test loss: 0.53447, Test acc: 73.10%
    Epoch: 3580 | Loss: 0.42597, Accuracy: 80.35% | Test loss: 0.53448, Test acc: 73.10%
    Epoch: 3590 | Loss: 0.42586, Accuracy: 80.35% | Test loss: 0.53448, Test acc: 73.10%
    Epoch: 3600 | Loss: 0.42575, Accuracy: 80.34% | Test loss: 0.53449, Test acc: 73.05%
    Epoch: 3610 | Loss: 0.42564, Accuracy: 80.34% | Test loss: 0.53450, Test acc: 73.05%
    Epoch: 3620 | Loss: 0.42553, Accuracy: 80.35% | Test loss: 0.53450, Test acc: 73.05%
    Epoch: 3630 | Loss: 0.42542, Accuracy: 80.35% | Test loss: 0.53451, Test acc: 73.10%
    Epoch: 3640 | Loss: 0.42531, Accuracy: 80.34% | Test loss: 0.53451, Test acc: 73.05%
    Epoch: 3650 | Loss: 0.42520, Accuracy: 80.35% | Test loss: 0.53451, Test acc: 73.05%
    Epoch: 3660 | Loss: 0.42509, Accuracy: 80.37% | Test loss: 0.53452, Test acc: 73.05%
    Epoch: 3670 | Loss: 0.42498, Accuracy: 80.37% | Test loss: 0.53452, Test acc: 73.05%
    Epoch: 3680 | Loss: 0.42487, Accuracy: 80.39% | Test loss: 0.53452, Test acc: 73.05%
    Epoch: 3690 | Loss: 0.42477, Accuracy: 80.39% | Test loss: 0.53453, Test acc: 73.05%
    Epoch: 3700 | Loss: 0.42466, Accuracy: 80.39% | Test loss: 0.53453, Test acc: 73.05%
    Epoch: 3710 | Loss: 0.42455, Accuracy: 80.42% | Test loss: 0.53453, Test acc: 73.05%
    Epoch: 3720 | Loss: 0.42444, Accuracy: 80.42% | Test loss: 0.53454, Test acc: 73.05%
    Epoch: 3730 | Loss: 0.42433, Accuracy: 80.42% | Test loss: 0.53454, Test acc: 73.05%
    Epoch: 3740 | Loss: 0.42422, Accuracy: 80.42% | Test loss: 0.53454, Test acc: 73.05%
    Epoch: 3750 | Loss: 0.42411, Accuracy: 80.43% | Test loss: 0.53455, Test acc: 73.05%
    Epoch: 3760 | Loss: 0.42400, Accuracy: 80.44% | Test loss: 0.53455, Test acc: 73.05%
    Epoch: 3770 | Loss: 0.42389, Accuracy: 80.46% | Test loss: 0.53455, Test acc: 73.05%
    Epoch: 3780 | Loss: 0.42378, Accuracy: 80.47% | Test loss: 0.53455, Test acc: 73.05%
    Epoch: 3790 | Loss: 0.42367, Accuracy: 80.47% | Test loss: 0.53455, Test acc: 73.05%
    Epoch: 3800 | Loss: 0.42356, Accuracy: 80.47% | Test loss: 0.53455, Test acc: 73.05%
    Epoch: 3810 | Loss: 0.42345, Accuracy: 80.48% | Test loss: 0.53455, Test acc: 73.05%
    Epoch: 3820 | Loss: 0.42334, Accuracy: 80.48% | Test loss: 0.53456, Test acc: 73.05%
    Epoch: 3830 | Loss: 0.42323, Accuracy: 80.48% | Test loss: 0.53456, Test acc: 73.05%
    Epoch: 3840 | Loss: 0.42312, Accuracy: 80.48% | Test loss: 0.53456, Test acc: 73.05%
    Epoch: 3850 | Loss: 0.42301, Accuracy: 80.50% | Test loss: 0.53457, Test acc: 73.05%
    Epoch: 3860 | Loss: 0.42290, Accuracy: 80.51% | Test loss: 0.53457, Test acc: 73.05%
    Epoch: 3870 | Loss: 0.42279, Accuracy: 80.50% | Test loss: 0.53457, Test acc: 73.05%
    Epoch: 3880 | Loss: 0.42268, Accuracy: 80.51% | Test loss: 0.53457, Test acc: 73.05%
    Epoch: 3890 | Loss: 0.42257, Accuracy: 80.51% | Test loss: 0.53457, Test acc: 73.05%
    Epoch: 3900 | Loss: 0.42246, Accuracy: 80.52% | Test loss: 0.53458, Test acc: 73.05%
    Epoch: 3910 | Loss: 0.42235, Accuracy: 80.53% | Test loss: 0.53458, Test acc: 73.15%
    Epoch: 3920 | Loss: 0.42224, Accuracy: 80.55% | Test loss: 0.53458, Test acc: 73.15%
    Epoch: 3930 | Loss: 0.42212, Accuracy: 80.55% | Test loss: 0.53458, Test acc: 73.15%
    Epoch: 3940 | Loss: 0.42201, Accuracy: 80.55% | Test loss: 0.53458, Test acc: 73.10%
    Epoch: 3950 | Loss: 0.42190, Accuracy: 80.56% | Test loss: 0.53458, Test acc: 73.10%
    Epoch: 3960 | Loss: 0.42179, Accuracy: 80.59% | Test loss: 0.53459, Test acc: 73.10%
    Epoch: 3970 | Loss: 0.42168, Accuracy: 80.60% | Test loss: 0.53459, Test acc: 73.10%
    Epoch: 3980 | Loss: 0.42157, Accuracy: 80.60% | Test loss: 0.53459, Test acc: 73.15%
    Epoch: 3990 | Loss: 0.42146, Accuracy: 80.60% | Test loss: 0.53460, Test acc: 73.21%
    Epoch: 4000 | Loss: 0.42135, Accuracy: 80.60% | Test loss: 0.53460, Test acc: 73.21%
    Epoch: 4010 | Loss: 0.42123, Accuracy: 80.61% | Test loss: 0.53461, Test acc: 73.21%
    Epoch: 4020 | Loss: 0.42112, Accuracy: 80.61% | Test loss: 0.53461, Test acc: 73.21%
    Epoch: 4030 | Loss: 0.42101, Accuracy: 80.62% | Test loss: 0.53462, Test acc: 73.21%
    Epoch: 4040 | Loss: 0.42090, Accuracy: 80.64% | Test loss: 0.53462, Test acc: 73.21%
    Epoch: 4050 | Loss: 0.42079, Accuracy: 80.64% | Test loss: 0.53463, Test acc: 73.21%
    Epoch: 4060 | Loss: 0.42068, Accuracy: 80.66% | Test loss: 0.53463, Test acc: 73.21%
    Epoch: 4070 | Loss: 0.42057, Accuracy: 80.65% | Test loss: 0.53464, Test acc: 73.21%
    Epoch: 4080 | Loss: 0.42045, Accuracy: 80.66% | Test loss: 0.53464, Test acc: 73.21%
    Epoch: 4090 | Loss: 0.42034, Accuracy: 80.68% | Test loss: 0.53465, Test acc: 73.21%
    Epoch: 4100 | Loss: 0.42023, Accuracy: 80.68% | Test loss: 0.53466, Test acc: 73.21%
    Epoch: 4110 | Loss: 0.42012, Accuracy: 80.69% | Test loss: 0.53466, Test acc: 73.26%
    Epoch: 4120 | Loss: 0.42001, Accuracy: 80.69% | Test loss: 0.53466, Test acc: 73.26%
    Epoch: 4130 | Loss: 0.41990, Accuracy: 80.69% | Test loss: 0.53467, Test acc: 73.31%
    Epoch: 4140 | Loss: 0.41978, Accuracy: 80.72% | Test loss: 0.53467, Test acc: 73.21%
    Epoch: 4150 | Loss: 0.41967, Accuracy: 80.73% | Test loss: 0.53467, Test acc: 73.26%
    Epoch: 4160 | Loss: 0.41956, Accuracy: 80.73% | Test loss: 0.53467, Test acc: 73.26%
    Epoch: 4170 | Loss: 0.41945, Accuracy: 80.73% | Test loss: 0.53467, Test acc: 73.26%
    Epoch: 4180 | Loss: 0.41934, Accuracy: 80.73% | Test loss: 0.53468, Test acc: 73.26%
    Epoch: 4190 | Loss: 0.41922, Accuracy: 80.75% | Test loss: 0.53468, Test acc: 73.26%
    Epoch: 4200 | Loss: 0.41911, Accuracy: 80.77% | Test loss: 0.53468, Test acc: 73.26%
    Epoch: 4210 | Loss: 0.41900, Accuracy: 80.78% | Test loss: 0.53469, Test acc: 73.26%
    Epoch: 4220 | Loss: 0.41889, Accuracy: 80.78% | Test loss: 0.53469, Test acc: 73.26%
    Epoch: 4230 | Loss: 0.41877, Accuracy: 80.78% | Test loss: 0.53469, Test acc: 73.26%
    Epoch: 4240 | Loss: 0.41866, Accuracy: 80.78% | Test loss: 0.53469, Test acc: 73.26%
    Epoch: 4250 | Loss: 0.41855, Accuracy: 80.78% | Test loss: 0.53469, Test acc: 73.26%
    Epoch: 4260 | Loss: 0.41844, Accuracy: 80.78% | Test loss: 0.53470, Test acc: 73.26%
    Epoch: 4270 | Loss: 0.41832, Accuracy: 80.79% | Test loss: 0.53470, Test acc: 73.31%
    Epoch: 4280 | Loss: 0.41821, Accuracy: 80.79% | Test loss: 0.53470, Test acc: 73.31%
    Epoch: 4290 | Loss: 0.41810, Accuracy: 80.79% | Test loss: 0.53470, Test acc: 73.31%
    Epoch: 4300 | Loss: 0.41798, Accuracy: 80.79% | Test loss: 0.53470, Test acc: 73.36%
    Epoch: 4310 | Loss: 0.41787, Accuracy: 80.79% | Test loss: 0.53470, Test acc: 73.36%
    Epoch: 4320 | Loss: 0.41776, Accuracy: 80.81% | Test loss: 0.53470, Test acc: 73.36%
    Epoch: 4330 | Loss: 0.41764, Accuracy: 80.81% | Test loss: 0.53470, Test acc: 73.36%
    Epoch: 4340 | Loss: 0.41753, Accuracy: 80.82% | Test loss: 0.53470, Test acc: 73.36%
    Epoch: 4350 | Loss: 0.41742, Accuracy: 80.82% | Test loss: 0.53470, Test acc: 73.36%
    Epoch: 4360 | Loss: 0.41731, Accuracy: 80.82% | Test loss: 0.53470, Test acc: 73.41%
    Epoch: 4370 | Loss: 0.41719, Accuracy: 80.82% | Test loss: 0.53470, Test acc: 73.41%
    Epoch: 4380 | Loss: 0.41708, Accuracy: 80.84% | Test loss: 0.53470, Test acc: 73.46%
    Epoch: 4390 | Loss: 0.41697, Accuracy: 80.86% | Test loss: 0.53470, Test acc: 73.41%
    Epoch: 4400 | Loss: 0.41685, Accuracy: 80.86% | Test loss: 0.53471, Test acc: 73.36%
    Epoch: 4410 | Loss: 0.41674, Accuracy: 80.86% | Test loss: 0.53471, Test acc: 73.36%
    Epoch: 4420 | Loss: 0.41662, Accuracy: 80.84% | Test loss: 0.53471, Test acc: 73.36%
    Epoch: 4430 | Loss: 0.41651, Accuracy: 80.84% | Test loss: 0.53472, Test acc: 73.36%
    Epoch: 4440 | Loss: 0.41640, Accuracy: 80.86% | Test loss: 0.53472, Test acc: 73.31%
    Epoch: 4450 | Loss: 0.41628, Accuracy: 80.90% | Test loss: 0.53473, Test acc: 73.31%
    Epoch: 4460 | Loss: 0.41617, Accuracy: 80.90% | Test loss: 0.53473, Test acc: 73.31%
    Epoch: 4470 | Loss: 0.41605, Accuracy: 80.91% | Test loss: 0.53474, Test acc: 73.31%
    Epoch: 4480 | Loss: 0.41594, Accuracy: 80.93% | Test loss: 0.53474, Test acc: 73.31%
    Epoch: 4490 | Loss: 0.41582, Accuracy: 80.96% | Test loss: 0.53475, Test acc: 73.36%
    Epoch: 4500 | Loss: 0.41571, Accuracy: 80.96% | Test loss: 0.53475, Test acc: 73.36%
    Epoch: 4510 | Loss: 0.41559, Accuracy: 80.96% | Test loss: 0.53476, Test acc: 73.36%
    Epoch: 4520 | Loss: 0.41548, Accuracy: 80.96% | Test loss: 0.53476, Test acc: 73.36%
    Epoch: 4530 | Loss: 0.41537, Accuracy: 80.96% | Test loss: 0.53477, Test acc: 73.36%
    Epoch: 4540 | Loss: 0.41525, Accuracy: 80.99% | Test loss: 0.53478, Test acc: 73.36%
    Epoch: 4550 | Loss: 0.41514, Accuracy: 80.99% | Test loss: 0.53478, Test acc: 73.31%
    Epoch: 4560 | Loss: 0.41502, Accuracy: 80.97% | Test loss: 0.53479, Test acc: 73.31%
    Epoch: 4570 | Loss: 0.41491, Accuracy: 80.97% | Test loss: 0.53479, Test acc: 73.31%
    Epoch: 4580 | Loss: 0.41479, Accuracy: 80.97% | Test loss: 0.53480, Test acc: 73.31%
    Epoch: 4590 | Loss: 0.41468, Accuracy: 80.97% | Test loss: 0.53480, Test acc: 73.31%
    Epoch: 4600 | Loss: 0.41456, Accuracy: 80.97% | Test loss: 0.53480, Test acc: 73.31%
    Epoch: 4610 | Loss: 0.41445, Accuracy: 80.95% | Test loss: 0.53481, Test acc: 73.31%
    Epoch: 4620 | Loss: 0.41433, Accuracy: 81.00% | Test loss: 0.53481, Test acc: 73.31%
    Epoch: 4630 | Loss: 0.41422, Accuracy: 81.00% | Test loss: 0.53482, Test acc: 73.31%
    Epoch: 4640 | Loss: 0.41410, Accuracy: 81.02% | Test loss: 0.53482, Test acc: 73.31%
    Epoch: 4650 | Loss: 0.41399, Accuracy: 81.04% | Test loss: 0.53483, Test acc: 73.36%
    Epoch: 4660 | Loss: 0.41387, Accuracy: 81.05% | Test loss: 0.53483, Test acc: 73.31%
    Epoch: 4670 | Loss: 0.41376, Accuracy: 81.05% | Test loss: 0.53483, Test acc: 73.31%
    Epoch: 4680 | Loss: 0.41364, Accuracy: 81.04% | Test loss: 0.53483, Test acc: 73.26%
    Epoch: 4690 | Loss: 0.41353, Accuracy: 81.05% | Test loss: 0.53483, Test acc: 73.26%
    Epoch: 4700 | Loss: 0.41341, Accuracy: 81.05% | Test loss: 0.53484, Test acc: 73.26%
    Epoch: 4710 | Loss: 0.41330, Accuracy: 81.05% | Test loss: 0.53484, Test acc: 73.26%
    Epoch: 4720 | Loss: 0.41318, Accuracy: 81.08% | Test loss: 0.53484, Test acc: 73.26%
    Epoch: 4730 | Loss: 0.41306, Accuracy: 81.08% | Test loss: 0.53484, Test acc: 73.36%
    Epoch: 4740 | Loss: 0.41295, Accuracy: 81.09% | Test loss: 0.53484, Test acc: 73.36%
    Epoch: 4750 | Loss: 0.41283, Accuracy: 81.09% | Test loss: 0.53484, Test acc: 73.31%
    Epoch: 4760 | Loss: 0.41272, Accuracy: 81.09% | Test loss: 0.53485, Test acc: 73.31%
    Epoch: 4770 | Loss: 0.41260, Accuracy: 81.10% | Test loss: 0.53485, Test acc: 73.31%
    Epoch: 4780 | Loss: 0.41248, Accuracy: 81.10% | Test loss: 0.53485, Test acc: 73.31%
    Epoch: 4790 | Loss: 0.41237, Accuracy: 81.13% | Test loss: 0.53486, Test acc: 73.31%
    Epoch: 4800 | Loss: 0.41225, Accuracy: 81.14% | Test loss: 0.53486, Test acc: 73.31%
    Epoch: 4810 | Loss: 0.41214, Accuracy: 81.14% | Test loss: 0.53487, Test acc: 73.31%
    Epoch: 4820 | Loss: 0.41202, Accuracy: 81.15% | Test loss: 0.53487, Test acc: 73.31%
    Epoch: 4830 | Loss: 0.41190, Accuracy: 81.17% | Test loss: 0.53487, Test acc: 73.31%
    Epoch: 4840 | Loss: 0.41179, Accuracy: 81.17% | Test loss: 0.53488, Test acc: 73.31%
    Epoch: 4850 | Loss: 0.41167, Accuracy: 81.15% | Test loss: 0.53488, Test acc: 73.31%
    Epoch: 4860 | Loss: 0.41155, Accuracy: 81.17% | Test loss: 0.53489, Test acc: 73.31%
    Epoch: 4870 | Loss: 0.41144, Accuracy: 81.22% | Test loss: 0.53489, Test acc: 73.31%
    Epoch: 4880 | Loss: 0.41132, Accuracy: 81.22% | Test loss: 0.53490, Test acc: 73.31%
    Epoch: 4890 | Loss: 0.41120, Accuracy: 81.22% | Test loss: 0.53490, Test acc: 73.31%
    Epoch: 4900 | Loss: 0.41109, Accuracy: 81.24% | Test loss: 0.53491, Test acc: 73.31%
    Epoch: 4910 | Loss: 0.41097, Accuracy: 81.26% | Test loss: 0.53491, Test acc: 73.31%
    Epoch: 4920 | Loss: 0.41085, Accuracy: 81.30% | Test loss: 0.53491, Test acc: 73.31%
    Epoch: 4930 | Loss: 0.41073, Accuracy: 81.32% | Test loss: 0.53491, Test acc: 73.26%
    Epoch: 4940 | Loss: 0.41062, Accuracy: 81.35% | Test loss: 0.53491, Test acc: 73.26%
    Epoch: 4950 | Loss: 0.41050, Accuracy: 81.35% | Test loss: 0.53491, Test acc: 73.26%
    Epoch: 4960 | Loss: 0.41038, Accuracy: 81.35% | Test loss: 0.53492, Test acc: 73.26%
    Epoch: 4970 | Loss: 0.41026, Accuracy: 81.39% | Test loss: 0.53492, Test acc: 73.31%
    Epoch: 4980 | Loss: 0.41015, Accuracy: 81.40% | Test loss: 0.53492, Test acc: 73.31%
    Epoch: 4990 | Loss: 0.41003, Accuracy: 81.40% | Test loss: 0.53492, Test acc: 73.36%
    

## Highlighting the difference gave slightly worse performance
This may be as we change the value to difference the model losses patterns that it had available before, I could try running the code with all the columns including these new ones but this has a lot of redundancy. 73.36% test accuracy to beat (3 runs of training)
