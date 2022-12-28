**CONTEXT**

The United Nations has hired you to support a week-long conference as their data specialist to help
analyze the results of the World Happiness Resort. The World Happiness Report, published by the UN
Sustainable Development Solutions Network, surveys countries across several quality-of-life
characteristics – such as economy, family, health, freedom, generosity, etc. – to determine a global
happiness ranking. The happiness score is used to rank countries and it is calculated by taking the sum of
all quality-of-life characteristic scores.


```python
%load_ext sql
```


```python
%sql sqlite:////Users/hochl/Downloads/apd_de_sql_screen.db
```


```sql
%%sql
--List all tables from the database
select name from sqlite_master
where type=='table'
```

     * sqlite:////Users/hochl/Downloads/apd_de_sql_screen.db
    Done.





<table>
    <tr>
        <th>name</th>
    </tr>
    <tr>
        <td>world_happiness_index_2015</td>
    </tr>
    <tr>
        <td>world_happiness_index_2016</td>
    </tr>
    <tr>
        <td>world_happiness_index_2017</td>
    </tr>
    <tr>
        <td>world_happiness_index_2018</td>
    </tr>
    <tr>
        <td>world_happiness_index_2019</td>
    </tr>
</table>



**TASKS**

Day 1 - You have been tasked with consolidating and cleaning the 5 available datasets into a SQL view
named `vw_world_happiness_index_consolidated`.

1. Add a year column.
2. Some years include extra quality-of-life characteristics. Replace null numeric values with zero.
3. Not all datasets include a Region field. Fill out the null region values with the correct region.
4. Which countries do not have a corresponding region?
5. What is the difference in code if wanting to include all countries regardless of having a corresponding region vs only including countries that have a corresponding region?
6. Include all countries regardless of having a corresponding region.
7. How many rows are in the consolidated view?

**1,2,3. Consolidating and cleaning the 5 available datasets**


```sql
%%sql

With 
distinct_region AS
    (
      select distinct country, region from world_happiness_index_2015
      union 
      select distinct country, region from world_happiness_index_2016
    )
select *, 2015 as 'year' from world_happiness_index_2015 
UNION ALL --2016
select *, 2016 as 'year' from world_happiness_index_2016
union all --2017
select 
      	w.country, d.region, happiness_rank, happiness_score, 
      	economy_gdp_per_capita, family, 0 as 'health_life_expectancy',
      	freedom, generosity, trust_government_corruption, dystopia_residual, 
      	2017 as 'year' 
from world_happiness_index_2017 w left join distinct_region d on w.country = d.country
UNION ALL --2018
select
     	w.country, d.region,
      	overall_rank as happiness_rank, 
      	score as happiness_score,
      	gdp_per_capita as economy_gdp_per_capita,
     	family, health_life_expectancy, freedom,
      	perceptions_of_corruption as trust_government_corruption,
        generosity,
      	0 as 'dystopia_residual',
      	2018 as 'year'
from world_happiness_index_2018 w  left join  distinct_region d on w.country = d.country
UNION ALL --2019
select
      	w.country, d.region,
      	overall_rank as happiness_rank, 
      	score as happiness_score,
      	gdp_per_capita as economy_gdp_per_capita,
     	family, health_life_expectancy, freedom,
      	perceptions_of_corruption as trust_government_corruption,
        generosity,
      	0 as 'dystopia_residual',
      	2019 as 'year'
from world_happiness_index_2019 w  left join distinct_region d on w.country = d.country
limit 100
```

     * sqlite:////Users/hochl/Downloads/apd_de_sql_screen.db
    Done.





<table>
    <tr>
        <th>country</th>
        <th>region</th>
        <th>happiness_rank</th>
        <th>happiness_score</th>
        <th>economy_gdp_per_capita</th>
        <th>family</th>
        <th>health_life_expectancy</th>
        <th>freedom</th>
        <th>trust_government_corruption</th>
        <th>generosity</th>
        <th>dystopia_residual</th>
        <th>year</th>
    </tr>
    <tr>
        <td>Switzerland</td>
        <td>Western Europe</td>
        <td>1</td>
        <td>7.587</td>
        <td>1.39651</td>
        <td>1.34951</td>
        <td>0.94143</td>
        <td>0.66557</td>
        <td>0.41978</td>
        <td>0.29678</td>
        <td>2.51738</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Iceland</td>
        <td>Western Europe</td>
        <td>2</td>
        <td>7.561</td>
        <td>1.30232</td>
        <td>1.40223</td>
        <td>0.94784</td>
        <td>0.62877</td>
        <td>0.14145</td>
        <td>0.4363</td>
        <td>2.70201</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Denmark</td>
        <td>Western Europe</td>
        <td>3</td>
        <td>7.527</td>
        <td>1.32548</td>
        <td>1.36058</td>
        <td>0.87464</td>
        <td>0.64938</td>
        <td>0.48357</td>
        <td>0.34139</td>
        <td>2.49204</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Norway</td>
        <td>Western Europe</td>
        <td>4</td>
        <td>7.522</td>
        <td>1.459</td>
        <td>1.33095</td>
        <td>0.88521</td>
        <td>0.66973</td>
        <td>0.36503</td>
        <td>0.34699</td>
        <td>2.46531</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Canada</td>
        <td>North America</td>
        <td>5</td>
        <td>7.427</td>
        <td>1.32629</td>
        <td>1.32261</td>
        <td>0.90563</td>
        <td>0.63297</td>
        <td>0.32957</td>
        <td>0.45811</td>
        <td>2.45176</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Finland</td>
        <td>Western Europe</td>
        <td>6</td>
        <td>7.406</td>
        <td>1.29025</td>
        <td>1.31826</td>
        <td>0.88911</td>
        <td>0.64169</td>
        <td>0.41372</td>
        <td>0.23351</td>
        <td>2.61955</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Netherlands</td>
        <td>Western Europe</td>
        <td>7</td>
        <td>7.378</td>
        <td>1.32944</td>
        <td>1.28017</td>
        <td>0.89284</td>
        <td>0.61576</td>
        <td>0.31814</td>
        <td>0.4761</td>
        <td>2.4657</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Sweden</td>
        <td>Western Europe</td>
        <td>8</td>
        <td>7.364</td>
        <td>1.33171</td>
        <td>1.28907</td>
        <td>0.91087</td>
        <td>0.6598</td>
        <td>0.43844</td>
        <td>0.36262</td>
        <td>2.37119</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>New Zealand</td>
        <td>Australia and New Zealand</td>
        <td>9</td>
        <td>7.286</td>
        <td>1.25018</td>
        <td>1.31967</td>
        <td>0.90837</td>
        <td>0.63938</td>
        <td>0.42922</td>
        <td>0.47501</td>
        <td>2.26425</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Australia</td>
        <td>Australia and New Zealand</td>
        <td>10</td>
        <td>7.284</td>
        <td>1.33358</td>
        <td>1.30923</td>
        <td>0.93156</td>
        <td>0.65124</td>
        <td>0.35637</td>
        <td>0.43562</td>
        <td>2.26646</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Israel</td>
        <td>Middle East and Northern Africa</td>
        <td>11</td>
        <td>7.278</td>
        <td>1.22857</td>
        <td>1.22393</td>
        <td>0.91387</td>
        <td>0.41319</td>
        <td>0.07785</td>
        <td>0.33172</td>
        <td>3.08854</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Costa Rica</td>
        <td>Latin America and Caribbean</td>
        <td>12</td>
        <td>7.226</td>
        <td>0.95578</td>
        <td>1.23788</td>
        <td>0.86027</td>
        <td>0.63376</td>
        <td>0.10583</td>
        <td>0.25497</td>
        <td>3.17728</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Austria</td>
        <td>Western Europe</td>
        <td>13</td>
        <td>7.2</td>
        <td>1.33723</td>
        <td>1.29704</td>
        <td>0.89042</td>
        <td>0.62433</td>
        <td>0.18676</td>
        <td>0.33088</td>
        <td>2.5332</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Mexico</td>
        <td>Latin America and Caribbean</td>
        <td>14</td>
        <td>7.187</td>
        <td>1.02054</td>
        <td>0.91451</td>
        <td>0.81444</td>
        <td>0.48181</td>
        <td>0.21312</td>
        <td>0.14074</td>
        <td>3.60214</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>United States</td>
        <td>North America</td>
        <td>15</td>
        <td>7.119</td>
        <td>1.39451</td>
        <td>1.24711</td>
        <td>0.86179</td>
        <td>0.54604</td>
        <td>0.1589</td>
        <td>0.40105</td>
        <td>2.51011</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Brazil</td>
        <td>Latin America and Caribbean</td>
        <td>16</td>
        <td>6.983</td>
        <td>0.98124</td>
        <td>1.23287</td>
        <td>0.69702</td>
        <td>0.49049</td>
        <td>0.17521</td>
        <td>0.14574</td>
        <td>3.26001</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Luxembourg</td>
        <td>Western Europe</td>
        <td>17</td>
        <td>6.946</td>
        <td>1.56391</td>
        <td>1.21963</td>
        <td>0.91894</td>
        <td>0.61583</td>
        <td>0.37798</td>
        <td>0.28034</td>
        <td>1.96961</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Ireland</td>
        <td>Western Europe</td>
        <td>18</td>
        <td>6.94</td>
        <td>1.33596</td>
        <td>1.36948</td>
        <td>0.89533</td>
        <td>0.61777</td>
        <td>0.28703</td>
        <td>0.45901</td>
        <td>1.9757</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Belgium</td>
        <td>Western Europe</td>
        <td>19</td>
        <td>6.937</td>
        <td>1.30782</td>
        <td>1.28566</td>
        <td>0.89667</td>
        <td>0.5845</td>
        <td>0.2254</td>
        <td>0.2225</td>
        <td>2.41484</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>United Arab Emirates</td>
        <td>Middle East and Northern Africa</td>
        <td>20</td>
        <td>6.901</td>
        <td>1.42727</td>
        <td>1.12575</td>
        <td>0.80925</td>
        <td>0.64157</td>
        <td>0.38583</td>
        <td>0.26428</td>
        <td>2.24743</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>United Kingdom</td>
        <td>Western Europe</td>
        <td>21</td>
        <td>6.867</td>
        <td>1.26637</td>
        <td>1.28548</td>
        <td>0.90943</td>
        <td>0.59625</td>
        <td>0.32067</td>
        <td>0.51912</td>
        <td>1.96994</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Oman</td>
        <td>Middle East and Northern Africa</td>
        <td>22</td>
        <td>6.853</td>
        <td>1.36011</td>
        <td>1.08182</td>
        <td>0.76276</td>
        <td>0.63274</td>
        <td>0.32524</td>
        <td>0.21542</td>
        <td>2.47489</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Venezuela</td>
        <td>Latin America and Caribbean</td>
        <td>23</td>
        <td>6.81</td>
        <td>1.04424</td>
        <td>1.25596</td>
        <td>0.72052</td>
        <td>0.42908</td>
        <td>0.11069</td>
        <td>0.05841</td>
        <td>3.19131</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Singapore</td>
        <td>Southeastern Asia</td>
        <td>24</td>
        <td>6.798</td>
        <td>1.52186</td>
        <td>1.02</td>
        <td>1.02525</td>
        <td>0.54252</td>
        <td>0.4921</td>
        <td>0.31105</td>
        <td>1.88501</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Panama</td>
        <td>Latin America and Caribbean</td>
        <td>25</td>
        <td>6.786</td>
        <td>1.06353</td>
        <td>1.1985</td>
        <td>0.79661</td>
        <td>0.5421</td>
        <td>0.0927</td>
        <td>0.24434</td>
        <td>2.84848</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Germany</td>
        <td>Western Europe</td>
        <td>26</td>
        <td>6.75</td>
        <td>1.32792</td>
        <td>1.29937</td>
        <td>0.89186</td>
        <td>0.61477</td>
        <td>0.21843</td>
        <td>0.28214</td>
        <td>2.11569</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Chile</td>
        <td>Latin America and Caribbean</td>
        <td>27</td>
        <td>6.67</td>
        <td>1.10715</td>
        <td>1.12447</td>
        <td>0.85857</td>
        <td>0.44132</td>
        <td>0.12869</td>
        <td>0.33363</td>
        <td>2.67585</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Qatar</td>
        <td>Middle East and Northern Africa</td>
        <td>28</td>
        <td>6.611</td>
        <td>1.69042</td>
        <td>1.0786</td>
        <td>0.79733</td>
        <td>0.6404</td>
        <td>0.52208</td>
        <td>0.32573</td>
        <td>1.55674</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>France</td>
        <td>Western Europe</td>
        <td>29</td>
        <td>6.575</td>
        <td>1.27778</td>
        <td>1.26038</td>
        <td>0.94579</td>
        <td>0.55011</td>
        <td>0.20646</td>
        <td>0.12332</td>
        <td>2.21126</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Argentina</td>
        <td>Latin America and Caribbean</td>
        <td>30</td>
        <td>6.574</td>
        <td>1.05351</td>
        <td>1.24823</td>
        <td>0.78723</td>
        <td>0.44974</td>
        <td>0.08484</td>
        <td>0.11451</td>
        <td>2.836</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Czech Republic</td>
        <td>Central and Eastern Europe</td>
        <td>31</td>
        <td>6.505</td>
        <td>1.17898</td>
        <td>1.20643</td>
        <td>0.84483</td>
        <td>0.46364</td>
        <td>0.02652</td>
        <td>0.10686</td>
        <td>2.67782</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Uruguay</td>
        <td>Latin America and Caribbean</td>
        <td>32</td>
        <td>6.485</td>
        <td>1.06166</td>
        <td>1.2089</td>
        <td>0.8116</td>
        <td>0.60362</td>
        <td>0.24558</td>
        <td>0.2324</td>
        <td>2.32142</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Colombia</td>
        <td>Latin America and Caribbean</td>
        <td>33</td>
        <td>6.477</td>
        <td>0.91861</td>
        <td>1.24018</td>
        <td>0.69077</td>
        <td>0.53466</td>
        <td>0.0512</td>
        <td>0.18401</td>
        <td>2.85737</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Thailand</td>
        <td>Southeastern Asia</td>
        <td>34</td>
        <td>6.455</td>
        <td>0.9669</td>
        <td>1.26504</td>
        <td>0.7385</td>
        <td>0.55664</td>
        <td>0.03187</td>
        <td>0.5763</td>
        <td>2.31945</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Saudi Arabia</td>
        <td>Middle East and Northern Africa</td>
        <td>35</td>
        <td>6.411</td>
        <td>1.39541</td>
        <td>1.08393</td>
        <td>0.72025</td>
        <td>0.31048</td>
        <td>0.32524</td>
        <td>0.13706</td>
        <td>2.43872</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Spain</td>
        <td>Western Europe</td>
        <td>36</td>
        <td>6.329</td>
        <td>1.23011</td>
        <td>1.31379</td>
        <td>0.95562</td>
        <td>0.45951</td>
        <td>0.06398</td>
        <td>0.18227</td>
        <td>2.12367</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Malta</td>
        <td>Western Europe</td>
        <td>37</td>
        <td>6.302</td>
        <td>1.2074</td>
        <td>1.30203</td>
        <td>0.88721</td>
        <td>0.60365</td>
        <td>0.13586</td>
        <td>0.51752</td>
        <td>1.6488</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Taiwan</td>
        <td>Eastern Asia</td>
        <td>38</td>
        <td>6.298</td>
        <td>1.29098</td>
        <td>1.07617</td>
        <td>0.8753</td>
        <td>0.3974</td>
        <td>0.08129</td>
        <td>0.25376</td>
        <td>2.32323</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Kuwait</td>
        <td>Middle East and Northern Africa</td>
        <td>39</td>
        <td>6.295</td>
        <td>1.55422</td>
        <td>1.16594</td>
        <td>0.72492</td>
        <td>0.55499</td>
        <td>0.25609</td>
        <td>0.16228</td>
        <td>1.87634</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Suriname</td>
        <td>Latin America and Caribbean</td>
        <td>40</td>
        <td>6.269</td>
        <td>0.99534</td>
        <td>0.972</td>
        <td>0.6082</td>
        <td>0.59657</td>
        <td>0.13633</td>
        <td>0.16991</td>
        <td>2.79094</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Trinidad and Tobago</td>
        <td>Latin America and Caribbean</td>
        <td>41</td>
        <td>6.168</td>
        <td>1.21183</td>
        <td>1.18354</td>
        <td>0.61483</td>
        <td>0.55884</td>
        <td>0.0114</td>
        <td>0.31844</td>
        <td>2.26882</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>El Salvador</td>
        <td>Latin America and Caribbean</td>
        <td>42</td>
        <td>6.13</td>
        <td>0.76454</td>
        <td>1.02507</td>
        <td>0.67737</td>
        <td>0.4035</td>
        <td>0.11776</td>
        <td>0.10692</td>
        <td>3.035</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Guatemala</td>
        <td>Latin America and Caribbean</td>
        <td>43</td>
        <td>6.123</td>
        <td>0.74553</td>
        <td>1.04356</td>
        <td>0.64425</td>
        <td>0.57733</td>
        <td>0.09472</td>
        <td>0.27489</td>
        <td>2.74255</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Uzbekistan</td>
        <td>Central and Eastern Europe</td>
        <td>44</td>
        <td>6.003</td>
        <td>0.63244</td>
        <td>1.34043</td>
        <td>0.59772</td>
        <td>0.65821</td>
        <td>0.30826</td>
        <td>0.22837</td>
        <td>2.23741</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Slovakia</td>
        <td>Central and Eastern Europe</td>
        <td>45</td>
        <td>5.995</td>
        <td>1.16891</td>
        <td>1.26999</td>
        <td>0.78902</td>
        <td>0.31751</td>
        <td>0.03431</td>
        <td>0.16893</td>
        <td>2.24639</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Japan</td>
        <td>Eastern Asia</td>
        <td>46</td>
        <td>5.987</td>
        <td>1.27074</td>
        <td>1.25712</td>
        <td>0.99111</td>
        <td>0.49615</td>
        <td>0.1806</td>
        <td>0.10705</td>
        <td>1.68435</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>South Korea</td>
        <td>Eastern Asia</td>
        <td>47</td>
        <td>5.984</td>
        <td>1.24461</td>
        <td>0.95774</td>
        <td>0.96538</td>
        <td>0.33208</td>
        <td>0.07857</td>
        <td>0.18557</td>
        <td>2.21978</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Ecuador</td>
        <td>Latin America and Caribbean</td>
        <td>48</td>
        <td>5.975</td>
        <td>0.86402</td>
        <td>0.99903</td>
        <td>0.79075</td>
        <td>0.48574</td>
        <td>0.1809</td>
        <td>0.11541</td>
        <td>2.53942</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Bahrain</td>
        <td>Middle East and Northern Africa</td>
        <td>49</td>
        <td>5.96</td>
        <td>1.32376</td>
        <td>1.21624</td>
        <td>0.74716</td>
        <td>0.45492</td>
        <td>0.306</td>
        <td>0.17362</td>
        <td>1.73797</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Italy</td>
        <td>Western Europe</td>
        <td>50</td>
        <td>5.948</td>
        <td>1.25114</td>
        <td>1.19777</td>
        <td>0.95446</td>
        <td>0.26236</td>
        <td>0.02901</td>
        <td>0.22823</td>
        <td>2.02518</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Bolivia</td>
        <td>Latin America and Caribbean</td>
        <td>51</td>
        <td>5.89</td>
        <td>0.68133</td>
        <td>0.97841</td>
        <td>0.5392</td>
        <td>0.57414</td>
        <td>0.088</td>
        <td>0.20536</td>
        <td>2.82334</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Moldova</td>
        <td>Central and Eastern Europe</td>
        <td>52</td>
        <td>5.889</td>
        <td>0.59448</td>
        <td>1.01528</td>
        <td>0.61826</td>
        <td>0.32818</td>
        <td>0.01615</td>
        <td>0.20951</td>
        <td>3.10712</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Paraguay</td>
        <td>Latin America and Caribbean</td>
        <td>53</td>
        <td>5.878</td>
        <td>0.75985</td>
        <td>1.30477</td>
        <td>0.66098</td>
        <td>0.53899</td>
        <td>0.08242</td>
        <td>0.3424</td>
        <td>2.18896</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Kazakhstan</td>
        <td>Central and Eastern Europe</td>
        <td>54</td>
        <td>5.855</td>
        <td>1.12254</td>
        <td>1.12241</td>
        <td>0.64368</td>
        <td>0.51649</td>
        <td>0.08454</td>
        <td>0.11827</td>
        <td>2.24729</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Slovenia</td>
        <td>Central and Eastern Europe</td>
        <td>55</td>
        <td>5.848</td>
        <td>1.18498</td>
        <td>1.27385</td>
        <td>0.87337</td>
        <td>0.60855</td>
        <td>0.03787</td>
        <td>0.25328</td>
        <td>1.61583</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Lithuania</td>
        <td>Central and Eastern Europe</td>
        <td>56</td>
        <td>5.833</td>
        <td>1.14723</td>
        <td>1.25745</td>
        <td>0.73128</td>
        <td>0.21342</td>
        <td>0.01031</td>
        <td>0.02641</td>
        <td>2.44649</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Nicaragua</td>
        <td>Latin America and Caribbean</td>
        <td>57</td>
        <td>5.828</td>
        <td>0.59325</td>
        <td>1.14184</td>
        <td>0.74314</td>
        <td>0.55475</td>
        <td>0.19317</td>
        <td>0.27815</td>
        <td>2.32407</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Peru</td>
        <td>Latin America and Caribbean</td>
        <td>58</td>
        <td>5.824</td>
        <td>0.90019</td>
        <td>0.97459</td>
        <td>0.73017</td>
        <td>0.41496</td>
        <td>0.05989</td>
        <td>0.14982</td>
        <td>2.5945</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Belarus</td>
        <td>Central and Eastern Europe</td>
        <td>59</td>
        <td>5.813</td>
        <td>1.03192</td>
        <td>1.23289</td>
        <td>0.73608</td>
        <td>0.37938</td>
        <td>0.1909</td>
        <td>0.11046</td>
        <td>2.1309</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Poland</td>
        <td>Central and Eastern Europe</td>
        <td>60</td>
        <td>5.791</td>
        <td>1.12555</td>
        <td>1.27948</td>
        <td>0.77903</td>
        <td>0.53122</td>
        <td>0.04212</td>
        <td>0.16759</td>
        <td>1.86565</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Malaysia</td>
        <td>Southeastern Asia</td>
        <td>61</td>
        <td>5.77</td>
        <td>1.12486</td>
        <td>1.07023</td>
        <td>0.72394</td>
        <td>0.53024</td>
        <td>0.10501</td>
        <td>0.33075</td>
        <td>1.88541</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Croatia</td>
        <td>Central and Eastern Europe</td>
        <td>62</td>
        <td>5.759</td>
        <td>1.08254</td>
        <td>0.79624</td>
        <td>0.78805</td>
        <td>0.25883</td>
        <td>0.0243</td>
        <td>0.05444</td>
        <td>2.75414</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Libya</td>
        <td>Middle East and Northern Africa</td>
        <td>63</td>
        <td>5.754</td>
        <td>1.13145</td>
        <td>1.11862</td>
        <td>0.7038</td>
        <td>0.41668</td>
        <td>0.11023</td>
        <td>0.18295</td>
        <td>2.09066</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Russia</td>
        <td>Central and Eastern Europe</td>
        <td>64</td>
        <td>5.716</td>
        <td>1.13764</td>
        <td>1.23617</td>
        <td>0.66926</td>
        <td>0.36679</td>
        <td>0.03005</td>
        <td>0.00199</td>
        <td>2.27394</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Jamaica</td>
        <td>Latin America and Caribbean</td>
        <td>65</td>
        <td>5.709</td>
        <td>0.81038</td>
        <td>1.15102</td>
        <td>0.68741</td>
        <td>0.50442</td>
        <td>0.02299</td>
        <td>0.2123</td>
        <td>2.32038</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>North Cyprus</td>
        <td>Western Europe</td>
        <td>66</td>
        <td>5.695</td>
        <td>1.20806</td>
        <td>1.07008</td>
        <td>0.92356</td>
        <td>0.49027</td>
        <td>0.1428</td>
        <td>0.26169</td>
        <td>1.59888</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Cyprus</td>
        <td>Western Europe</td>
        <td>67</td>
        <td>5.689</td>
        <td>1.20813</td>
        <td>0.89318</td>
        <td>0.92356</td>
        <td>0.40672</td>
        <td>0.06146</td>
        <td>0.30638</td>
        <td>1.88931</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Algeria</td>
        <td>Middle East and Northern Africa</td>
        <td>68</td>
        <td>5.605</td>
        <td>0.93929</td>
        <td>1.07772</td>
        <td>0.61766</td>
        <td>0.28579</td>
        <td>0.17383</td>
        <td>0.07822</td>
        <td>2.43209</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Kosovo</td>
        <td>Central and Eastern Europe</td>
        <td>69</td>
        <td>5.589</td>
        <td>0.80148</td>
        <td>0.81198</td>
        <td>0.63132</td>
        <td>0.24749</td>
        <td>0.04741</td>
        <td>0.2831</td>
        <td>2.76579</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Turkmenistan</td>
        <td>Central and Eastern Europe</td>
        <td>70</td>
        <td>5.548</td>
        <td>0.95847</td>
        <td>1.22668</td>
        <td>0.53886</td>
        <td>0.4761</td>
        <td>0.30844</td>
        <td>0.16979</td>
        <td>1.86984</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Mauritius</td>
        <td>Sub-Saharan Africa</td>
        <td>71</td>
        <td>5.477</td>
        <td>1.00761</td>
        <td>0.98521</td>
        <td>0.7095</td>
        <td>0.56066</td>
        <td>0.07521</td>
        <td>0.37744</td>
        <td>1.76145</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Hong Kong</td>
        <td>Eastern Asia</td>
        <td>72</td>
        <td>5.474</td>
        <td>1.38604</td>
        <td>1.05818</td>
        <td>1.01328</td>
        <td>0.59608</td>
        <td>0.37124</td>
        <td>0.39478</td>
        <td>0.65429</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Estonia</td>
        <td>Central and Eastern Europe</td>
        <td>73</td>
        <td>5.429</td>
        <td>1.15174</td>
        <td>1.22791</td>
        <td>0.77361</td>
        <td>0.44888</td>
        <td>0.15184</td>
        <td>0.0868</td>
        <td>1.58782</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Indonesia</td>
        <td>Southeastern Asia</td>
        <td>74</td>
        <td>5.399</td>
        <td>0.82827</td>
        <td>1.08708</td>
        <td>0.63793</td>
        <td>0.46611</td>
        <td>0.0</td>
        <td>0.51535</td>
        <td>1.86399</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Vietnam</td>
        <td>Southeastern Asia</td>
        <td>75</td>
        <td>5.36</td>
        <td>0.63216</td>
        <td>0.91226</td>
        <td>0.74676</td>
        <td>0.59444</td>
        <td>0.10441</td>
        <td>0.1686</td>
        <td>2.20173</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Turkey</td>
        <td>Middle East and Northern Africa</td>
        <td>76</td>
        <td>5.332</td>
        <td>1.06098</td>
        <td>0.94632</td>
        <td>0.73172</td>
        <td>0.22815</td>
        <td>0.15746</td>
        <td>0.12253</td>
        <td>2.08528</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Kyrgyzstan</td>
        <td>Central and Eastern Europe</td>
        <td>77</td>
        <td>5.286</td>
        <td>0.47428</td>
        <td>1.15115</td>
        <td>0.65088</td>
        <td>0.43477</td>
        <td>0.04232</td>
        <td>0.3003</td>
        <td>2.2327</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Nigeria</td>
        <td>Sub-Saharan Africa</td>
        <td>78</td>
        <td>5.268</td>
        <td>0.65435</td>
        <td>0.90432</td>
        <td>0.16007</td>
        <td>0.34334</td>
        <td>0.0403</td>
        <td>0.27233</td>
        <td>2.89319</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Bhutan</td>
        <td>Southern Asia</td>
        <td>79</td>
        <td>5.253</td>
        <td>0.77042</td>
        <td>1.10395</td>
        <td>0.57407</td>
        <td>0.53206</td>
        <td>0.15445</td>
        <td>0.47998</td>
        <td>1.63794</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Azerbaijan</td>
        <td>Central and Eastern Europe</td>
        <td>80</td>
        <td>5.212</td>
        <td>1.02389</td>
        <td>0.93793</td>
        <td>0.64045</td>
        <td>0.3703</td>
        <td>0.16065</td>
        <td>0.07799</td>
        <td>2.00073</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Pakistan</td>
        <td>Southern Asia</td>
        <td>81</td>
        <td>5.194</td>
        <td>0.59543</td>
        <td>0.41411</td>
        <td>0.51466</td>
        <td>0.12102</td>
        <td>0.10464</td>
        <td>0.33671</td>
        <td>3.10709</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Jordan</td>
        <td>Middle East and Northern Africa</td>
        <td>82</td>
        <td>5.192</td>
        <td>0.90198</td>
        <td>1.05392</td>
        <td>0.69639</td>
        <td>0.40661</td>
        <td>0.14293</td>
        <td>0.11053</td>
        <td>1.87996</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Montenegro</td>
        <td>Central and Eastern Europe</td>
        <td>82</td>
        <td>5.192</td>
        <td>0.97438</td>
        <td>0.90557</td>
        <td>0.72521</td>
        <td>0.1826</td>
        <td>0.14296</td>
        <td>0.1614</td>
        <td>2.10017</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>China</td>
        <td>Eastern Asia</td>
        <td>84</td>
        <td>5.14</td>
        <td>0.89012</td>
        <td>0.94675</td>
        <td>0.81658</td>
        <td>0.51697</td>
        <td>0.02781</td>
        <td>0.08185</td>
        <td>1.8604</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Zambia</td>
        <td>Sub-Saharan Africa</td>
        <td>85</td>
        <td>5.129</td>
        <td>0.47038</td>
        <td>0.91612</td>
        <td>0.29924</td>
        <td>0.48827</td>
        <td>0.12468</td>
        <td>0.19591</td>
        <td>2.6343</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Romania</td>
        <td>Central and Eastern Europe</td>
        <td>86</td>
        <td>5.124</td>
        <td>1.04345</td>
        <td>0.88588</td>
        <td>0.7689</td>
        <td>0.35068</td>
        <td>0.00649</td>
        <td>0.13748</td>
        <td>1.93129</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Serbia</td>
        <td>Central and Eastern Europe</td>
        <td>87</td>
        <td>5.123</td>
        <td>0.92053</td>
        <td>1.00964</td>
        <td>0.74836</td>
        <td>0.20107</td>
        <td>0.02617</td>
        <td>0.19231</td>
        <td>2.025</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Portugal</td>
        <td>Western Europe</td>
        <td>88</td>
        <td>5.102</td>
        <td>1.15991</td>
        <td>1.13935</td>
        <td>0.87519</td>
        <td>0.51469</td>
        <td>0.01078</td>
        <td>0.13719</td>
        <td>1.26462</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Latvia</td>
        <td>Central and Eastern Europe</td>
        <td>89</td>
        <td>5.098</td>
        <td>1.11312</td>
        <td>1.09562</td>
        <td>0.72437</td>
        <td>0.29671</td>
        <td>0.06332</td>
        <td>0.18226</td>
        <td>1.62215</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Philippines</td>
        <td>Southeastern Asia</td>
        <td>90</td>
        <td>5.073</td>
        <td>0.70532</td>
        <td>1.03516</td>
        <td>0.58114</td>
        <td>0.62545</td>
        <td>0.12279</td>
        <td>0.24991</td>
        <td>1.7536</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Somaliland region</td>
        <td>Sub-Saharan Africa</td>
        <td>91</td>
        <td>5.057</td>
        <td>0.18847</td>
        <td>0.95152</td>
        <td>0.43873</td>
        <td>0.46582</td>
        <td>0.39928</td>
        <td>0.50318</td>
        <td>2.11032</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Morocco</td>
        <td>Middle East and Northern Africa</td>
        <td>92</td>
        <td>5.013</td>
        <td>0.73479</td>
        <td>0.64095</td>
        <td>0.60954</td>
        <td>0.41691</td>
        <td>0.08546</td>
        <td>0.07172</td>
        <td>2.45373</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Macedonia</td>
        <td>Central and Eastern Europe</td>
        <td>93</td>
        <td>5.007</td>
        <td>0.91851</td>
        <td>1.00232</td>
        <td>0.73545</td>
        <td>0.33457</td>
        <td>0.05327</td>
        <td>0.22359</td>
        <td>1.73933</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Mozambique</td>
        <td>Sub-Saharan Africa</td>
        <td>94</td>
        <td>4.971</td>
        <td>0.08308</td>
        <td>1.02626</td>
        <td>0.09131</td>
        <td>0.34037</td>
        <td>0.15603</td>
        <td>0.22269</td>
        <td>3.05137</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Albania</td>
        <td>Central and Eastern Europe</td>
        <td>95</td>
        <td>4.959</td>
        <td>0.87867</td>
        <td>0.80434</td>
        <td>0.81325</td>
        <td>0.35733</td>
        <td>0.06413</td>
        <td>0.14272</td>
        <td>1.89894</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Bosnia and Herzegovina</td>
        <td>Central and Eastern Europe</td>
        <td>96</td>
        <td>4.949</td>
        <td>0.83223</td>
        <td>0.91916</td>
        <td>0.79081</td>
        <td>0.09245</td>
        <td>0.00227</td>
        <td>0.24808</td>
        <td>2.06367</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Lesotho</td>
        <td>Sub-Saharan Africa</td>
        <td>97</td>
        <td>4.898</td>
        <td>0.37545</td>
        <td>1.04103</td>
        <td>0.07612</td>
        <td>0.31767</td>
        <td>0.12504</td>
        <td>0.16388</td>
        <td>2.79832</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Dominican Republic</td>
        <td>Latin America and Caribbean</td>
        <td>98</td>
        <td>4.885</td>
        <td>0.89537</td>
        <td>1.17202</td>
        <td>0.66825</td>
        <td>0.57672</td>
        <td>0.14234</td>
        <td>0.21684</td>
        <td>1.21305</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Laos</td>
        <td>Southeastern Asia</td>
        <td>99</td>
        <td>4.876</td>
        <td>0.59066</td>
        <td>0.73803</td>
        <td>0.54909</td>
        <td>0.59591</td>
        <td>0.24249</td>
        <td>0.42192</td>
        <td>1.73799</td>
        <td>2015</td>
    </tr>
    <tr>
        <td>Mongolia</td>
        <td>Eastern Asia</td>
        <td>100</td>
        <td>4.874</td>
        <td>0.82819</td>
        <td>1.3006</td>
        <td>0.60268</td>
        <td>0.43626</td>
        <td>0.02666</td>
        <td>0.3323</td>
        <td>1.34759</td>
        <td>2015</td>
    </tr>
</table>



**4. Which countries do not have a corresponding region?**


```sql
%%sql
select
    distinct country
from vw_world_happiness_index_consolidated
where region is null;
```

     * sqlite:////Users/hochl/Downloads/apd_de_sql_screen.db
    Done.





<table>
    <tr>
        <th>country</th>
    </tr>
    <tr>
        <td>Taiwan Province of China</td>
    </tr>
    <tr>
        <td>Hong Kong S.A.R., China</td>
    </tr>
    <tr>
        <td>Trinidad &amp; Tobago</td>
    </tr>
    <tr>
        <td>Northern Cyprus</td>
    </tr>
    <tr>
        <td>North Macedonia</td>
    </tr>
    <tr>
        <td>Gambia</td>
    </tr>
</table>



**5,6. Exclude countries without corresponding** 

JOIN rather than LEFT JOIN 

JOIN (excluding): 774 rows
LEFT JOIN (including): 782 rows 

**7.How many rows are in the consolidated view?**


```sql
%%sql
select
    count(country) as row_num
from vw_world_happiness_index_consolidated;
```

     * sqlite:////Users/hochl/Downloads/apd_de_sql_screen.db
    Done.





<table>
    <tr>
        <th>row_num</th>
    </tr>
    <tr>
        <td>782</td>
    </tr>
</table>



Day 2 – In order to prepare this dataset for dashboard use, the quality-of-life characteristics need to be
unpivoted from columns to rows. Create another SQL view named `vw_world_happiness_index` with
the following columns: year, country, region, happiness_ranking, happiness_score, characteristic, value.


```sql
%%sql

select -- economy_gdp_per_capita
      year,
      country,
      region,
      happiness_rank as happiness_ranking,
      happiness_score,
      'economy_gdp_per_capita' as characteristic,
      economy_gdp_per_capita as value
from vw_world_happiness_index_consolidated
UNION ALL
select --family
      year,
      country,
      region,
      happiness_rank as happiness_ranking,
      happiness_score,
      'family' as characteristic,
      family as value
from vw_world_happiness_index_consolidated
UNION ALL
select --health_life_expectancy
      year,
      country,
      region,
      happiness_rank as happiness_ranking,
      happiness_score,
      'health_life_expectancy' as characteristic,
      health_life_expectancy as value
from vw_world_happiness_index_consolidated
UNION ALL
select --freedom
      year,
      country,
      region,
      happiness_rank as happiness_ranking,
      happiness_score,
      'freedom' as characteristic,
      freedom as value
from vw_world_happiness_index_consolidated
UNION ALL
select --generosity
      year,
      country,
      region,
      happiness_rank as happiness_ranking,
      happiness_score,
      'generosity' as characteristic,
      generosity as value
from vw_world_happiness_index_consolidated
UNION ALL
select --trust_government_corruption
      year,
      country,
      region,
      happiness_rank as happiness_ranking,
      happiness_score,
      'trust_government_corruption' as characteristic,
      trust_government_corruption as value
from vw_world_happiness_index_consolidated
UNION ALL
select --dystopia_residual
      year,
      country,
      region,
      happiness_rank as happiness_ranking,
      happiness_score,
      'dystopia_residual' as characteristic,
      dystopia_residual as value
from vw_world_happiness_index_consolidated
limit 500
```

     * sqlite:////Users/hochl/Downloads/apd_de_sql_screen.db
    Done.





<table>
    <tr>
        <th>year</th>
        <th>country</th>
        <th>region</th>
        <th>happiness_ranking</th>
        <th>happiness_score</th>
        <th>characteristic</th>
        <th>value</th>
    </tr>
    <tr>
        <td>2015</td>
        <td>Switzerland</td>
        <td>Western Europe</td>
        <td>1</td>
        <td>7.587</td>
        <td>economy_gdp_per_capita</td>
        <td>1.39651</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Iceland</td>
        <td>Western Europe</td>
        <td>2</td>
        <td>7.561</td>
        <td>economy_gdp_per_capita</td>
        <td>1.30232</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Denmark</td>
        <td>Western Europe</td>
        <td>3</td>
        <td>7.527</td>
        <td>economy_gdp_per_capita</td>
        <td>1.32548</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Norway</td>
        <td>Western Europe</td>
        <td>4</td>
        <td>7.522</td>
        <td>economy_gdp_per_capita</td>
        <td>1.459</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Canada</td>
        <td>North America</td>
        <td>5</td>
        <td>7.427</td>
        <td>economy_gdp_per_capita</td>
        <td>1.32629</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Finland</td>
        <td>Western Europe</td>
        <td>6</td>
        <td>7.406</td>
        <td>economy_gdp_per_capita</td>
        <td>1.29025</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Netherlands</td>
        <td>Western Europe</td>
        <td>7</td>
        <td>7.378</td>
        <td>economy_gdp_per_capita</td>
        <td>1.32944</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Sweden</td>
        <td>Western Europe</td>
        <td>8</td>
        <td>7.364</td>
        <td>economy_gdp_per_capita</td>
        <td>1.33171</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>New Zealand</td>
        <td>Australia and New Zealand</td>
        <td>9</td>
        <td>7.286</td>
        <td>economy_gdp_per_capita</td>
        <td>1.25018</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Australia</td>
        <td>Australia and New Zealand</td>
        <td>10</td>
        <td>7.284</td>
        <td>economy_gdp_per_capita</td>
        <td>1.33358</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Israel</td>
        <td>Middle East and Northern Africa</td>
        <td>11</td>
        <td>7.278</td>
        <td>economy_gdp_per_capita</td>
        <td>1.22857</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Costa Rica</td>
        <td>Latin America and Caribbean</td>
        <td>12</td>
        <td>7.226</td>
        <td>economy_gdp_per_capita</td>
        <td>0.95578</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Austria</td>
        <td>Western Europe</td>
        <td>13</td>
        <td>7.2</td>
        <td>economy_gdp_per_capita</td>
        <td>1.33723</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Mexico</td>
        <td>Latin America and Caribbean</td>
        <td>14</td>
        <td>7.187</td>
        <td>economy_gdp_per_capita</td>
        <td>1.02054</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>United States</td>
        <td>North America</td>
        <td>15</td>
        <td>7.119</td>
        <td>economy_gdp_per_capita</td>
        <td>1.39451</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Brazil</td>
        <td>Latin America and Caribbean</td>
        <td>16</td>
        <td>6.983</td>
        <td>economy_gdp_per_capita</td>
        <td>0.98124</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Luxembourg</td>
        <td>Western Europe</td>
        <td>17</td>
        <td>6.946</td>
        <td>economy_gdp_per_capita</td>
        <td>1.56391</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Ireland</td>
        <td>Western Europe</td>
        <td>18</td>
        <td>6.94</td>
        <td>economy_gdp_per_capita</td>
        <td>1.33596</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Belgium</td>
        <td>Western Europe</td>
        <td>19</td>
        <td>6.937</td>
        <td>economy_gdp_per_capita</td>
        <td>1.30782</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>United Arab Emirates</td>
        <td>Middle East and Northern Africa</td>
        <td>20</td>
        <td>6.901</td>
        <td>economy_gdp_per_capita</td>
        <td>1.42727</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>United Kingdom</td>
        <td>Western Europe</td>
        <td>21</td>
        <td>6.867</td>
        <td>economy_gdp_per_capita</td>
        <td>1.26637</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Oman</td>
        <td>Middle East and Northern Africa</td>
        <td>22</td>
        <td>6.853</td>
        <td>economy_gdp_per_capita</td>
        <td>1.36011</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Venezuela</td>
        <td>Latin America and Caribbean</td>
        <td>23</td>
        <td>6.81</td>
        <td>economy_gdp_per_capita</td>
        <td>1.04424</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Singapore</td>
        <td>Southeastern Asia</td>
        <td>24</td>
        <td>6.798</td>
        <td>economy_gdp_per_capita</td>
        <td>1.52186</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Panama</td>
        <td>Latin America and Caribbean</td>
        <td>25</td>
        <td>6.786</td>
        <td>economy_gdp_per_capita</td>
        <td>1.06353</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Germany</td>
        <td>Western Europe</td>
        <td>26</td>
        <td>6.75</td>
        <td>economy_gdp_per_capita</td>
        <td>1.32792</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Chile</td>
        <td>Latin America and Caribbean</td>
        <td>27</td>
        <td>6.67</td>
        <td>economy_gdp_per_capita</td>
        <td>1.10715</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Qatar</td>
        <td>Middle East and Northern Africa</td>
        <td>28</td>
        <td>6.611</td>
        <td>economy_gdp_per_capita</td>
        <td>1.69042</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>France</td>
        <td>Western Europe</td>
        <td>29</td>
        <td>6.575</td>
        <td>economy_gdp_per_capita</td>
        <td>1.27778</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Argentina</td>
        <td>Latin America and Caribbean</td>
        <td>30</td>
        <td>6.574</td>
        <td>economy_gdp_per_capita</td>
        <td>1.05351</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Czech Republic</td>
        <td>Central and Eastern Europe</td>
        <td>31</td>
        <td>6.505</td>
        <td>economy_gdp_per_capita</td>
        <td>1.17898</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Uruguay</td>
        <td>Latin America and Caribbean</td>
        <td>32</td>
        <td>6.485</td>
        <td>economy_gdp_per_capita</td>
        <td>1.06166</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Colombia</td>
        <td>Latin America and Caribbean</td>
        <td>33</td>
        <td>6.477</td>
        <td>economy_gdp_per_capita</td>
        <td>0.91861</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Thailand</td>
        <td>Southeastern Asia</td>
        <td>34</td>
        <td>6.455</td>
        <td>economy_gdp_per_capita</td>
        <td>0.9669</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Saudi Arabia</td>
        <td>Middle East and Northern Africa</td>
        <td>35</td>
        <td>6.411</td>
        <td>economy_gdp_per_capita</td>
        <td>1.39541</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Spain</td>
        <td>Western Europe</td>
        <td>36</td>
        <td>6.329</td>
        <td>economy_gdp_per_capita</td>
        <td>1.23011</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Malta</td>
        <td>Western Europe</td>
        <td>37</td>
        <td>6.302</td>
        <td>economy_gdp_per_capita</td>
        <td>1.2074</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Taiwan</td>
        <td>Eastern Asia</td>
        <td>38</td>
        <td>6.298</td>
        <td>economy_gdp_per_capita</td>
        <td>1.29098</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Kuwait</td>
        <td>Middle East and Northern Africa</td>
        <td>39</td>
        <td>6.295</td>
        <td>economy_gdp_per_capita</td>
        <td>1.55422</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Suriname</td>
        <td>Latin America and Caribbean</td>
        <td>40</td>
        <td>6.269</td>
        <td>economy_gdp_per_capita</td>
        <td>0.99534</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Trinidad and Tobago</td>
        <td>Latin America and Caribbean</td>
        <td>41</td>
        <td>6.168</td>
        <td>economy_gdp_per_capita</td>
        <td>1.21183</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>El Salvador</td>
        <td>Latin America and Caribbean</td>
        <td>42</td>
        <td>6.13</td>
        <td>economy_gdp_per_capita</td>
        <td>0.76454</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Guatemala</td>
        <td>Latin America and Caribbean</td>
        <td>43</td>
        <td>6.123</td>
        <td>economy_gdp_per_capita</td>
        <td>0.74553</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Uzbekistan</td>
        <td>Central and Eastern Europe</td>
        <td>44</td>
        <td>6.003</td>
        <td>economy_gdp_per_capita</td>
        <td>0.63244</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Slovakia</td>
        <td>Central and Eastern Europe</td>
        <td>45</td>
        <td>5.995</td>
        <td>economy_gdp_per_capita</td>
        <td>1.16891</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Japan</td>
        <td>Eastern Asia</td>
        <td>46</td>
        <td>5.987</td>
        <td>economy_gdp_per_capita</td>
        <td>1.27074</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>South Korea</td>
        <td>Eastern Asia</td>
        <td>47</td>
        <td>5.984</td>
        <td>economy_gdp_per_capita</td>
        <td>1.24461</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Ecuador</td>
        <td>Latin America and Caribbean</td>
        <td>48</td>
        <td>5.975</td>
        <td>economy_gdp_per_capita</td>
        <td>0.86402</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Bahrain</td>
        <td>Middle East and Northern Africa</td>
        <td>49</td>
        <td>5.96</td>
        <td>economy_gdp_per_capita</td>
        <td>1.32376</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Italy</td>
        <td>Western Europe</td>
        <td>50</td>
        <td>5.948</td>
        <td>economy_gdp_per_capita</td>
        <td>1.25114</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Bolivia</td>
        <td>Latin America and Caribbean</td>
        <td>51</td>
        <td>5.89</td>
        <td>economy_gdp_per_capita</td>
        <td>0.68133</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Moldova</td>
        <td>Central and Eastern Europe</td>
        <td>52</td>
        <td>5.889</td>
        <td>economy_gdp_per_capita</td>
        <td>0.59448</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Paraguay</td>
        <td>Latin America and Caribbean</td>
        <td>53</td>
        <td>5.878</td>
        <td>economy_gdp_per_capita</td>
        <td>0.75985</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Kazakhstan</td>
        <td>Central and Eastern Europe</td>
        <td>54</td>
        <td>5.855</td>
        <td>economy_gdp_per_capita</td>
        <td>1.12254</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Slovenia</td>
        <td>Central and Eastern Europe</td>
        <td>55</td>
        <td>5.848</td>
        <td>economy_gdp_per_capita</td>
        <td>1.18498</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Lithuania</td>
        <td>Central and Eastern Europe</td>
        <td>56</td>
        <td>5.833</td>
        <td>economy_gdp_per_capita</td>
        <td>1.14723</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Nicaragua</td>
        <td>Latin America and Caribbean</td>
        <td>57</td>
        <td>5.828</td>
        <td>economy_gdp_per_capita</td>
        <td>0.59325</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Peru</td>
        <td>Latin America and Caribbean</td>
        <td>58</td>
        <td>5.824</td>
        <td>economy_gdp_per_capita</td>
        <td>0.90019</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Belarus</td>
        <td>Central and Eastern Europe</td>
        <td>59</td>
        <td>5.813</td>
        <td>economy_gdp_per_capita</td>
        <td>1.03192</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Poland</td>
        <td>Central and Eastern Europe</td>
        <td>60</td>
        <td>5.791</td>
        <td>economy_gdp_per_capita</td>
        <td>1.12555</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Malaysia</td>
        <td>Southeastern Asia</td>
        <td>61</td>
        <td>5.77</td>
        <td>economy_gdp_per_capita</td>
        <td>1.12486</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Croatia</td>
        <td>Central and Eastern Europe</td>
        <td>62</td>
        <td>5.759</td>
        <td>economy_gdp_per_capita</td>
        <td>1.08254</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Libya</td>
        <td>Middle East and Northern Africa</td>
        <td>63</td>
        <td>5.754</td>
        <td>economy_gdp_per_capita</td>
        <td>1.13145</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Russia</td>
        <td>Central and Eastern Europe</td>
        <td>64</td>
        <td>5.716</td>
        <td>economy_gdp_per_capita</td>
        <td>1.13764</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Jamaica</td>
        <td>Latin America and Caribbean</td>
        <td>65</td>
        <td>5.709</td>
        <td>economy_gdp_per_capita</td>
        <td>0.81038</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>North Cyprus</td>
        <td>Western Europe</td>
        <td>66</td>
        <td>5.695</td>
        <td>economy_gdp_per_capita</td>
        <td>1.20806</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Cyprus</td>
        <td>Western Europe</td>
        <td>67</td>
        <td>5.689</td>
        <td>economy_gdp_per_capita</td>
        <td>1.20813</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Algeria</td>
        <td>Middle East and Northern Africa</td>
        <td>68</td>
        <td>5.605</td>
        <td>economy_gdp_per_capita</td>
        <td>0.93929</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Kosovo</td>
        <td>Central and Eastern Europe</td>
        <td>69</td>
        <td>5.589</td>
        <td>economy_gdp_per_capita</td>
        <td>0.80148</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Turkmenistan</td>
        <td>Central and Eastern Europe</td>
        <td>70</td>
        <td>5.548</td>
        <td>economy_gdp_per_capita</td>
        <td>0.95847</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Mauritius</td>
        <td>Sub-Saharan Africa</td>
        <td>71</td>
        <td>5.477</td>
        <td>economy_gdp_per_capita</td>
        <td>1.00761</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Hong Kong</td>
        <td>Eastern Asia</td>
        <td>72</td>
        <td>5.474</td>
        <td>economy_gdp_per_capita</td>
        <td>1.38604</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Estonia</td>
        <td>Central and Eastern Europe</td>
        <td>73</td>
        <td>5.429</td>
        <td>economy_gdp_per_capita</td>
        <td>1.15174</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Indonesia</td>
        <td>Southeastern Asia</td>
        <td>74</td>
        <td>5.399</td>
        <td>economy_gdp_per_capita</td>
        <td>0.82827</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Vietnam</td>
        <td>Southeastern Asia</td>
        <td>75</td>
        <td>5.36</td>
        <td>economy_gdp_per_capita</td>
        <td>0.63216</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Turkey</td>
        <td>Middle East and Northern Africa</td>
        <td>76</td>
        <td>5.332</td>
        <td>economy_gdp_per_capita</td>
        <td>1.06098</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Kyrgyzstan</td>
        <td>Central and Eastern Europe</td>
        <td>77</td>
        <td>5.286</td>
        <td>economy_gdp_per_capita</td>
        <td>0.47428</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Nigeria</td>
        <td>Sub-Saharan Africa</td>
        <td>78</td>
        <td>5.268</td>
        <td>economy_gdp_per_capita</td>
        <td>0.65435</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Bhutan</td>
        <td>Southern Asia</td>
        <td>79</td>
        <td>5.253</td>
        <td>economy_gdp_per_capita</td>
        <td>0.77042</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Azerbaijan</td>
        <td>Central and Eastern Europe</td>
        <td>80</td>
        <td>5.212</td>
        <td>economy_gdp_per_capita</td>
        <td>1.02389</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Pakistan</td>
        <td>Southern Asia</td>
        <td>81</td>
        <td>5.194</td>
        <td>economy_gdp_per_capita</td>
        <td>0.59543</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Jordan</td>
        <td>Middle East and Northern Africa</td>
        <td>82</td>
        <td>5.192</td>
        <td>economy_gdp_per_capita</td>
        <td>0.90198</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Montenegro</td>
        <td>Central and Eastern Europe</td>
        <td>82</td>
        <td>5.192</td>
        <td>economy_gdp_per_capita</td>
        <td>0.97438</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>China</td>
        <td>Eastern Asia</td>
        <td>84</td>
        <td>5.14</td>
        <td>economy_gdp_per_capita</td>
        <td>0.89012</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Zambia</td>
        <td>Sub-Saharan Africa</td>
        <td>85</td>
        <td>5.129</td>
        <td>economy_gdp_per_capita</td>
        <td>0.47038</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Romania</td>
        <td>Central and Eastern Europe</td>
        <td>86</td>
        <td>5.124</td>
        <td>economy_gdp_per_capita</td>
        <td>1.04345</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Serbia</td>
        <td>Central and Eastern Europe</td>
        <td>87</td>
        <td>5.123</td>
        <td>economy_gdp_per_capita</td>
        <td>0.92053</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Portugal</td>
        <td>Western Europe</td>
        <td>88</td>
        <td>5.102</td>
        <td>economy_gdp_per_capita</td>
        <td>1.15991</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Latvia</td>
        <td>Central and Eastern Europe</td>
        <td>89</td>
        <td>5.098</td>
        <td>economy_gdp_per_capita</td>
        <td>1.11312</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Philippines</td>
        <td>Southeastern Asia</td>
        <td>90</td>
        <td>5.073</td>
        <td>economy_gdp_per_capita</td>
        <td>0.70532</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Somaliland region</td>
        <td>Sub-Saharan Africa</td>
        <td>91</td>
        <td>5.057</td>
        <td>economy_gdp_per_capita</td>
        <td>0.18847</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Morocco</td>
        <td>Middle East and Northern Africa</td>
        <td>92</td>
        <td>5.013</td>
        <td>economy_gdp_per_capita</td>
        <td>0.73479</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Macedonia</td>
        <td>Central and Eastern Europe</td>
        <td>93</td>
        <td>5.007</td>
        <td>economy_gdp_per_capita</td>
        <td>0.91851</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Mozambique</td>
        <td>Sub-Saharan Africa</td>
        <td>94</td>
        <td>4.971</td>
        <td>economy_gdp_per_capita</td>
        <td>0.08308</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Albania</td>
        <td>Central and Eastern Europe</td>
        <td>95</td>
        <td>4.959</td>
        <td>economy_gdp_per_capita</td>
        <td>0.87867</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Bosnia and Herzegovina</td>
        <td>Central and Eastern Europe</td>
        <td>96</td>
        <td>4.949</td>
        <td>economy_gdp_per_capita</td>
        <td>0.83223</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Lesotho</td>
        <td>Sub-Saharan Africa</td>
        <td>97</td>
        <td>4.898</td>
        <td>economy_gdp_per_capita</td>
        <td>0.37545</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Dominican Republic</td>
        <td>Latin America and Caribbean</td>
        <td>98</td>
        <td>4.885</td>
        <td>economy_gdp_per_capita</td>
        <td>0.89537</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Laos</td>
        <td>Southeastern Asia</td>
        <td>99</td>
        <td>4.876</td>
        <td>economy_gdp_per_capita</td>
        <td>0.59066</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Mongolia</td>
        <td>Eastern Asia</td>
        <td>100</td>
        <td>4.874</td>
        <td>economy_gdp_per_capita</td>
        <td>0.82819</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Swaziland</td>
        <td>Sub-Saharan Africa</td>
        <td>101</td>
        <td>4.867</td>
        <td>economy_gdp_per_capita</td>
        <td>0.71206</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Greece</td>
        <td>Western Europe</td>
        <td>102</td>
        <td>4.857</td>
        <td>economy_gdp_per_capita</td>
        <td>1.15406</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Lebanon</td>
        <td>Middle East and Northern Africa</td>
        <td>103</td>
        <td>4.839</td>
        <td>economy_gdp_per_capita</td>
        <td>1.02564</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Hungary</td>
        <td>Central and Eastern Europe</td>
        <td>104</td>
        <td>4.8</td>
        <td>economy_gdp_per_capita</td>
        <td>1.12094</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Honduras</td>
        <td>Latin America and Caribbean</td>
        <td>105</td>
        <td>4.788</td>
        <td>economy_gdp_per_capita</td>
        <td>0.59532</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Tajikistan</td>
        <td>Central and Eastern Europe</td>
        <td>106</td>
        <td>4.786</td>
        <td>economy_gdp_per_capita</td>
        <td>0.39047</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Tunisia</td>
        <td>Middle East and Northern Africa</td>
        <td>107</td>
        <td>4.739</td>
        <td>economy_gdp_per_capita</td>
        <td>0.88113</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Palestinian Territories</td>
        <td>Middle East and Northern Africa</td>
        <td>108</td>
        <td>4.715</td>
        <td>economy_gdp_per_capita</td>
        <td>0.59867</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Bangladesh</td>
        <td>Southern Asia</td>
        <td>109</td>
        <td>4.694</td>
        <td>economy_gdp_per_capita</td>
        <td>0.39753</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Iran</td>
        <td>Middle East and Northern Africa</td>
        <td>110</td>
        <td>4.686</td>
        <td>economy_gdp_per_capita</td>
        <td>1.0088</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Ukraine</td>
        <td>Central and Eastern Europe</td>
        <td>111</td>
        <td>4.681</td>
        <td>economy_gdp_per_capita</td>
        <td>0.79907</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Iraq</td>
        <td>Middle East and Northern Africa</td>
        <td>112</td>
        <td>4.677</td>
        <td>economy_gdp_per_capita</td>
        <td>0.98549</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>South Africa</td>
        <td>Sub-Saharan Africa</td>
        <td>113</td>
        <td>4.642</td>
        <td>economy_gdp_per_capita</td>
        <td>0.92049</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Ghana</td>
        <td>Sub-Saharan Africa</td>
        <td>114</td>
        <td>4.633</td>
        <td>economy_gdp_per_capita</td>
        <td>0.54558</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Zimbabwe</td>
        <td>Sub-Saharan Africa</td>
        <td>115</td>
        <td>4.61</td>
        <td>economy_gdp_per_capita</td>
        <td>0.271</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Liberia</td>
        <td>Sub-Saharan Africa</td>
        <td>116</td>
        <td>4.571</td>
        <td>economy_gdp_per_capita</td>
        <td>0.0712</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>India</td>
        <td>Southern Asia</td>
        <td>117</td>
        <td>4.565</td>
        <td>economy_gdp_per_capita</td>
        <td>0.64499</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Sudan</td>
        <td>Sub-Saharan Africa</td>
        <td>118</td>
        <td>4.55</td>
        <td>economy_gdp_per_capita</td>
        <td>0.52107</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Haiti</td>
        <td>Latin America and Caribbean</td>
        <td>119</td>
        <td>4.518</td>
        <td>economy_gdp_per_capita</td>
        <td>0.26673</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Congo (Kinshasa)</td>
        <td>Sub-Saharan Africa</td>
        <td>120</td>
        <td>4.517</td>
        <td>economy_gdp_per_capita</td>
        <td>0.0</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Nepal</td>
        <td>Southern Asia</td>
        <td>121</td>
        <td>4.514</td>
        <td>economy_gdp_per_capita</td>
        <td>0.35997</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Ethiopia</td>
        <td>Sub-Saharan Africa</td>
        <td>122</td>
        <td>4.512</td>
        <td>economy_gdp_per_capita</td>
        <td>0.19073</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Sierra Leone</td>
        <td>Sub-Saharan Africa</td>
        <td>123</td>
        <td>4.507</td>
        <td>economy_gdp_per_capita</td>
        <td>0.33024</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Mauritania</td>
        <td>Sub-Saharan Africa</td>
        <td>124</td>
        <td>4.436</td>
        <td>economy_gdp_per_capita</td>
        <td>0.45407</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Kenya</td>
        <td>Sub-Saharan Africa</td>
        <td>125</td>
        <td>4.419</td>
        <td>economy_gdp_per_capita</td>
        <td>0.36471</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Djibouti</td>
        <td>Sub-Saharan Africa</td>
        <td>126</td>
        <td>4.369</td>
        <td>economy_gdp_per_capita</td>
        <td>0.44025</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Armenia</td>
        <td>Central and Eastern Europe</td>
        <td>127</td>
        <td>4.35</td>
        <td>economy_gdp_per_capita</td>
        <td>0.76821</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Botswana</td>
        <td>Sub-Saharan Africa</td>
        <td>128</td>
        <td>4.332</td>
        <td>economy_gdp_per_capita</td>
        <td>0.99355</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Myanmar</td>
        <td>Southeastern Asia</td>
        <td>129</td>
        <td>4.307</td>
        <td>economy_gdp_per_capita</td>
        <td>0.27108</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Georgia</td>
        <td>Central and Eastern Europe</td>
        <td>130</td>
        <td>4.297</td>
        <td>economy_gdp_per_capita</td>
        <td>0.7419</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Malawi</td>
        <td>Sub-Saharan Africa</td>
        <td>131</td>
        <td>4.292</td>
        <td>economy_gdp_per_capita</td>
        <td>0.01604</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Sri Lanka</td>
        <td>Southern Asia</td>
        <td>132</td>
        <td>4.271</td>
        <td>economy_gdp_per_capita</td>
        <td>0.83524</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Cameroon</td>
        <td>Sub-Saharan Africa</td>
        <td>133</td>
        <td>4.252</td>
        <td>economy_gdp_per_capita</td>
        <td>0.4225</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Bulgaria</td>
        <td>Central and Eastern Europe</td>
        <td>134</td>
        <td>4.218</td>
        <td>economy_gdp_per_capita</td>
        <td>1.01216</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Egypt</td>
        <td>Middle East and Northern Africa</td>
        <td>135</td>
        <td>4.194</td>
        <td>economy_gdp_per_capita</td>
        <td>0.8818</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Yemen</td>
        <td>Middle East and Northern Africa</td>
        <td>136</td>
        <td>4.077</td>
        <td>economy_gdp_per_capita</td>
        <td>0.54649</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Angola</td>
        <td>Sub-Saharan Africa</td>
        <td>137</td>
        <td>4.033</td>
        <td>economy_gdp_per_capita</td>
        <td>0.75778</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Mali</td>
        <td>Sub-Saharan Africa</td>
        <td>138</td>
        <td>3.995</td>
        <td>economy_gdp_per_capita</td>
        <td>0.26074</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Congo (Brazzaville)</td>
        <td>Sub-Saharan Africa</td>
        <td>139</td>
        <td>3.989</td>
        <td>economy_gdp_per_capita</td>
        <td>0.67866</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Comoros</td>
        <td>Sub-Saharan Africa</td>
        <td>140</td>
        <td>3.956</td>
        <td>economy_gdp_per_capita</td>
        <td>0.23906</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Uganda</td>
        <td>Sub-Saharan Africa</td>
        <td>141</td>
        <td>3.931</td>
        <td>economy_gdp_per_capita</td>
        <td>0.21102</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Senegal</td>
        <td>Sub-Saharan Africa</td>
        <td>142</td>
        <td>3.904</td>
        <td>economy_gdp_per_capita</td>
        <td>0.36498</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Gabon</td>
        <td>Sub-Saharan Africa</td>
        <td>143</td>
        <td>3.896</td>
        <td>economy_gdp_per_capita</td>
        <td>1.06024</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Niger</td>
        <td>Sub-Saharan Africa</td>
        <td>144</td>
        <td>3.845</td>
        <td>economy_gdp_per_capita</td>
        <td>0.0694</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Cambodia</td>
        <td>Southeastern Asia</td>
        <td>145</td>
        <td>3.819</td>
        <td>economy_gdp_per_capita</td>
        <td>0.46038</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Tanzania</td>
        <td>Sub-Saharan Africa</td>
        <td>146</td>
        <td>3.781</td>
        <td>economy_gdp_per_capita</td>
        <td>0.2852</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Madagascar</td>
        <td>Sub-Saharan Africa</td>
        <td>147</td>
        <td>3.681</td>
        <td>economy_gdp_per_capita</td>
        <td>0.20824</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Central African Republic</td>
        <td>Sub-Saharan Africa</td>
        <td>148</td>
        <td>3.678</td>
        <td>economy_gdp_per_capita</td>
        <td>0.0785</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Chad</td>
        <td>Sub-Saharan Africa</td>
        <td>149</td>
        <td>3.667</td>
        <td>economy_gdp_per_capita</td>
        <td>0.34193</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Guinea</td>
        <td>Sub-Saharan Africa</td>
        <td>150</td>
        <td>3.656</td>
        <td>economy_gdp_per_capita</td>
        <td>0.17417</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Ivory Coast</td>
        <td>Sub-Saharan Africa</td>
        <td>151</td>
        <td>3.655</td>
        <td>economy_gdp_per_capita</td>
        <td>0.46534</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Burkina Faso</td>
        <td>Sub-Saharan Africa</td>
        <td>152</td>
        <td>3.587</td>
        <td>economy_gdp_per_capita</td>
        <td>0.25812</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Afghanistan</td>
        <td>Southern Asia</td>
        <td>153</td>
        <td>3.575</td>
        <td>economy_gdp_per_capita</td>
        <td>0.31982</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Rwanda</td>
        <td>Sub-Saharan Africa</td>
        <td>154</td>
        <td>3.465</td>
        <td>economy_gdp_per_capita</td>
        <td>0.22208</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Benin</td>
        <td>Sub-Saharan Africa</td>
        <td>155</td>
        <td>3.34</td>
        <td>economy_gdp_per_capita</td>
        <td>0.28665</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Syria</td>
        <td>Middle East and Northern Africa</td>
        <td>156</td>
        <td>3.006</td>
        <td>economy_gdp_per_capita</td>
        <td>0.6632</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Burundi</td>
        <td>Sub-Saharan Africa</td>
        <td>157</td>
        <td>2.905</td>
        <td>economy_gdp_per_capita</td>
        <td>0.0153</td>
    </tr>
    <tr>
        <td>2015</td>
        <td>Togo</td>
        <td>Sub-Saharan Africa</td>
        <td>158</td>
        <td>2.839</td>
        <td>economy_gdp_per_capita</td>
        <td>0.20868</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Denmark</td>
        <td>Western Europe</td>
        <td>1</td>
        <td>7.526</td>
        <td>economy_gdp_per_capita</td>
        <td>1.44178</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Switzerland</td>
        <td>Western Europe</td>
        <td>2</td>
        <td>7.509</td>
        <td>economy_gdp_per_capita</td>
        <td>1.52733</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Iceland</td>
        <td>Western Europe</td>
        <td>3</td>
        <td>7.501</td>
        <td>economy_gdp_per_capita</td>
        <td>1.42666</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Norway</td>
        <td>Western Europe</td>
        <td>4</td>
        <td>7.498</td>
        <td>economy_gdp_per_capita</td>
        <td>1.57744</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Finland</td>
        <td>Western Europe</td>
        <td>5</td>
        <td>7.413</td>
        <td>economy_gdp_per_capita</td>
        <td>1.40598</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Canada</td>
        <td>North America</td>
        <td>6</td>
        <td>7.404</td>
        <td>economy_gdp_per_capita</td>
        <td>1.44015</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Netherlands</td>
        <td>Western Europe</td>
        <td>7</td>
        <td>7.339</td>
        <td>economy_gdp_per_capita</td>
        <td>1.46468</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>New Zealand</td>
        <td>Australia and New Zealand</td>
        <td>8</td>
        <td>7.334</td>
        <td>economy_gdp_per_capita</td>
        <td>1.36066</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Australia</td>
        <td>Australia and New Zealand</td>
        <td>9</td>
        <td>7.313</td>
        <td>economy_gdp_per_capita</td>
        <td>1.44443</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Sweden</td>
        <td>Western Europe</td>
        <td>10</td>
        <td>7.291</td>
        <td>economy_gdp_per_capita</td>
        <td>1.45181</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Israel</td>
        <td>Middle East and Northern Africa</td>
        <td>11</td>
        <td>7.267</td>
        <td>economy_gdp_per_capita</td>
        <td>1.33766</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Austria</td>
        <td>Western Europe</td>
        <td>12</td>
        <td>7.119</td>
        <td>economy_gdp_per_capita</td>
        <td>1.45038</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>United States</td>
        <td>North America</td>
        <td>13</td>
        <td>7.104</td>
        <td>economy_gdp_per_capita</td>
        <td>1.50796</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Costa Rica</td>
        <td>Latin America and Caribbean</td>
        <td>14</td>
        <td>7.087</td>
        <td>economy_gdp_per_capita</td>
        <td>1.06879</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Puerto Rico</td>
        <td>Latin America and Caribbean</td>
        <td>15</td>
        <td>7.039</td>
        <td>economy_gdp_per_capita</td>
        <td>1.35943</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Germany</td>
        <td>Western Europe</td>
        <td>16</td>
        <td>6.994</td>
        <td>economy_gdp_per_capita</td>
        <td>1.44787</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Brazil</td>
        <td>Latin America and Caribbean</td>
        <td>17</td>
        <td>6.952</td>
        <td>economy_gdp_per_capita</td>
        <td>1.08754</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Belgium</td>
        <td>Western Europe</td>
        <td>18</td>
        <td>6.929</td>
        <td>economy_gdp_per_capita</td>
        <td>1.42539</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Ireland</td>
        <td>Western Europe</td>
        <td>19</td>
        <td>6.907</td>
        <td>economy_gdp_per_capita</td>
        <td>1.48341</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Luxembourg</td>
        <td>Western Europe</td>
        <td>20</td>
        <td>6.871</td>
        <td>economy_gdp_per_capita</td>
        <td>1.69752</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Mexico</td>
        <td>Latin America and Caribbean</td>
        <td>21</td>
        <td>6.778</td>
        <td>economy_gdp_per_capita</td>
        <td>1.11508</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Singapore</td>
        <td>Southeastern Asia</td>
        <td>22</td>
        <td>6.739</td>
        <td>economy_gdp_per_capita</td>
        <td>1.64555</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>United Kingdom</td>
        <td>Western Europe</td>
        <td>23</td>
        <td>6.725</td>
        <td>economy_gdp_per_capita</td>
        <td>1.40283</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Chile</td>
        <td>Latin America and Caribbean</td>
        <td>24</td>
        <td>6.705</td>
        <td>economy_gdp_per_capita</td>
        <td>1.2167</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Panama</td>
        <td>Latin America and Caribbean</td>
        <td>25</td>
        <td>6.701</td>
        <td>economy_gdp_per_capita</td>
        <td>1.18306</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Argentina</td>
        <td>Latin America and Caribbean</td>
        <td>26</td>
        <td>6.65</td>
        <td>economy_gdp_per_capita</td>
        <td>1.15137</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Czech Republic</td>
        <td>Central and Eastern Europe</td>
        <td>27</td>
        <td>6.596</td>
        <td>economy_gdp_per_capita</td>
        <td>1.30915</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>United Arab Emirates</td>
        <td>Middle East and Northern Africa</td>
        <td>28</td>
        <td>6.573</td>
        <td>economy_gdp_per_capita</td>
        <td>1.57352</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Uruguay</td>
        <td>Latin America and Caribbean</td>
        <td>29</td>
        <td>6.545</td>
        <td>economy_gdp_per_capita</td>
        <td>1.18157</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Malta</td>
        <td>Western Europe</td>
        <td>30</td>
        <td>6.488</td>
        <td>economy_gdp_per_capita</td>
        <td>1.30782</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Colombia</td>
        <td>Latin America and Caribbean</td>
        <td>31</td>
        <td>6.481</td>
        <td>economy_gdp_per_capita</td>
        <td>1.03032</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>France</td>
        <td>Western Europe</td>
        <td>32</td>
        <td>6.478</td>
        <td>economy_gdp_per_capita</td>
        <td>1.39488</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Thailand</td>
        <td>Southeastern Asia</td>
        <td>33</td>
        <td>6.474</td>
        <td>economy_gdp_per_capita</td>
        <td>1.0893</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Saudi Arabia</td>
        <td>Middle East and Northern Africa</td>
        <td>34</td>
        <td>6.379</td>
        <td>economy_gdp_per_capita</td>
        <td>1.48953</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Taiwan</td>
        <td>Eastern Asia</td>
        <td>34</td>
        <td>6.379</td>
        <td>economy_gdp_per_capita</td>
        <td>1.39729</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Qatar</td>
        <td>Middle East and Northern Africa</td>
        <td>36</td>
        <td>6.375</td>
        <td>economy_gdp_per_capita</td>
        <td>1.82427</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Spain</td>
        <td>Western Europe</td>
        <td>37</td>
        <td>6.361</td>
        <td>economy_gdp_per_capita</td>
        <td>1.34253</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Algeria</td>
        <td>Middle East and Northern Africa</td>
        <td>38</td>
        <td>6.355</td>
        <td>economy_gdp_per_capita</td>
        <td>1.05266</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Guatemala</td>
        <td>Latin America and Caribbean</td>
        <td>39</td>
        <td>6.324</td>
        <td>economy_gdp_per_capita</td>
        <td>0.83454</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Suriname</td>
        <td>Latin America and Caribbean</td>
        <td>40</td>
        <td>6.269</td>
        <td>economy_gdp_per_capita</td>
        <td>1.09686</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Kuwait</td>
        <td>Middle East and Northern Africa</td>
        <td>41</td>
        <td>6.239</td>
        <td>economy_gdp_per_capita</td>
        <td>1.61714</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Bahrain</td>
        <td>Middle East and Northern Africa</td>
        <td>42</td>
        <td>6.218</td>
        <td>economy_gdp_per_capita</td>
        <td>1.44024</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Trinidad and Tobago</td>
        <td>Latin America and Caribbean</td>
        <td>43</td>
        <td>6.168</td>
        <td>economy_gdp_per_capita</td>
        <td>1.32572</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Venezuela</td>
        <td>Latin America and Caribbean</td>
        <td>44</td>
        <td>6.084</td>
        <td>economy_gdp_per_capita</td>
        <td>1.13367</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Slovakia</td>
        <td>Central and Eastern Europe</td>
        <td>45</td>
        <td>6.078</td>
        <td>economy_gdp_per_capita</td>
        <td>1.27973</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>El Salvador</td>
        <td>Latin America and Caribbean</td>
        <td>46</td>
        <td>6.068</td>
        <td>economy_gdp_per_capita</td>
        <td>0.8737</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Malaysia</td>
        <td>Southeastern Asia</td>
        <td>47</td>
        <td>6.005</td>
        <td>economy_gdp_per_capita</td>
        <td>1.25142</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Nicaragua</td>
        <td>Latin America and Caribbean</td>
        <td>48</td>
        <td>5.992</td>
        <td>economy_gdp_per_capita</td>
        <td>0.69384</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Uzbekistan</td>
        <td>Central and Eastern Europe</td>
        <td>49</td>
        <td>5.987</td>
        <td>economy_gdp_per_capita</td>
        <td>0.73591</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Italy</td>
        <td>Western Europe</td>
        <td>50</td>
        <td>5.977</td>
        <td>economy_gdp_per_capita</td>
        <td>1.35495</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Ecuador</td>
        <td>Latin America and Caribbean</td>
        <td>51</td>
        <td>5.976</td>
        <td>economy_gdp_per_capita</td>
        <td>0.97306</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Belize</td>
        <td>Latin America and Caribbean</td>
        <td>52</td>
        <td>5.956</td>
        <td>economy_gdp_per_capita</td>
        <td>0.87616</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Japan</td>
        <td>Eastern Asia</td>
        <td>53</td>
        <td>5.921</td>
        <td>economy_gdp_per_capita</td>
        <td>1.38007</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Kazakhstan</td>
        <td>Central and Eastern Europe</td>
        <td>54</td>
        <td>5.919</td>
        <td>economy_gdp_per_capita</td>
        <td>1.22943</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Moldova</td>
        <td>Central and Eastern Europe</td>
        <td>55</td>
        <td>5.897</td>
        <td>economy_gdp_per_capita</td>
        <td>0.69177</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Russia</td>
        <td>Central and Eastern Europe</td>
        <td>56</td>
        <td>5.856</td>
        <td>economy_gdp_per_capita</td>
        <td>1.23228</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Poland</td>
        <td>Central and Eastern Europe</td>
        <td>57</td>
        <td>5.835</td>
        <td>economy_gdp_per_capita</td>
        <td>1.24585</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>South Korea</td>
        <td>Eastern Asia</td>
        <td>57</td>
        <td>5.835</td>
        <td>economy_gdp_per_capita</td>
        <td>1.35948</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Bolivia</td>
        <td>Latin America and Caribbean</td>
        <td>59</td>
        <td>5.822</td>
        <td>economy_gdp_per_capita</td>
        <td>0.79422</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Lithuania</td>
        <td>Central and Eastern Europe</td>
        <td>60</td>
        <td>5.813</td>
        <td>economy_gdp_per_capita</td>
        <td>1.2692</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Belarus</td>
        <td>Central and Eastern Europe</td>
        <td>61</td>
        <td>5.802</td>
        <td>economy_gdp_per_capita</td>
        <td>1.13062</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>North Cyprus</td>
        <td>Western Europe</td>
        <td>62</td>
        <td>5.771</td>
        <td>economy_gdp_per_capita</td>
        <td>1.31141</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Slovenia</td>
        <td>Central and Eastern Europe</td>
        <td>63</td>
        <td>5.768</td>
        <td>economy_gdp_per_capita</td>
        <td>1.29947</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Peru</td>
        <td>Latin America and Caribbean</td>
        <td>64</td>
        <td>5.743</td>
        <td>economy_gdp_per_capita</td>
        <td>0.99602</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Turkmenistan</td>
        <td>Central and Eastern Europe</td>
        <td>65</td>
        <td>5.658</td>
        <td>economy_gdp_per_capita</td>
        <td>1.08017</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Mauritius</td>
        <td>Sub-Saharan Africa</td>
        <td>66</td>
        <td>5.648</td>
        <td>economy_gdp_per_capita</td>
        <td>1.14372</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Libya</td>
        <td>Middle East and Northern Africa</td>
        <td>67</td>
        <td>5.615</td>
        <td>economy_gdp_per_capita</td>
        <td>1.06688</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Latvia</td>
        <td>Central and Eastern Europe</td>
        <td>68</td>
        <td>5.56</td>
        <td>economy_gdp_per_capita</td>
        <td>1.21788</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Cyprus</td>
        <td>Western Europe</td>
        <td>69</td>
        <td>5.546</td>
        <td>economy_gdp_per_capita</td>
        <td>1.31857</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Paraguay</td>
        <td>Latin America and Caribbean</td>
        <td>70</td>
        <td>5.538</td>
        <td>economy_gdp_per_capita</td>
        <td>0.89373</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Romania</td>
        <td>Central and Eastern Europe</td>
        <td>71</td>
        <td>5.528</td>
        <td>economy_gdp_per_capita</td>
        <td>1.1697</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Estonia</td>
        <td>Central and Eastern Europe</td>
        <td>72</td>
        <td>5.517</td>
        <td>economy_gdp_per_capita</td>
        <td>1.27964</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Jamaica</td>
        <td>Latin America and Caribbean</td>
        <td>73</td>
        <td>5.51</td>
        <td>economy_gdp_per_capita</td>
        <td>0.89333</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Croatia</td>
        <td>Central and Eastern Europe</td>
        <td>74</td>
        <td>5.488</td>
        <td>economy_gdp_per_capita</td>
        <td>1.18649</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Hong Kong</td>
        <td>Eastern Asia</td>
        <td>75</td>
        <td>5.458</td>
        <td>economy_gdp_per_capita</td>
        <td>1.5107</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Somalia</td>
        <td>Sub-Saharan Africa</td>
        <td>76</td>
        <td>5.44</td>
        <td>economy_gdp_per_capita</td>
        <td>0.0</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Kosovo</td>
        <td>Central and Eastern Europe</td>
        <td>77</td>
        <td>5.401</td>
        <td>economy_gdp_per_capita</td>
        <td>0.90145</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Turkey</td>
        <td>Middle East and Northern Africa</td>
        <td>78</td>
        <td>5.389</td>
        <td>economy_gdp_per_capita</td>
        <td>1.16492</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Indonesia</td>
        <td>Southeastern Asia</td>
        <td>79</td>
        <td>5.314</td>
        <td>economy_gdp_per_capita</td>
        <td>0.95104</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Jordan</td>
        <td>Middle East and Northern Africa</td>
        <td>80</td>
        <td>5.303</td>
        <td>economy_gdp_per_capita</td>
        <td>0.99673</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Azerbaijan</td>
        <td>Central and Eastern Europe</td>
        <td>81</td>
        <td>5.291</td>
        <td>economy_gdp_per_capita</td>
        <td>1.12373</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Philippines</td>
        <td>Southeastern Asia</td>
        <td>82</td>
        <td>5.279</td>
        <td>economy_gdp_per_capita</td>
        <td>0.81217</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>China</td>
        <td>Eastern Asia</td>
        <td>83</td>
        <td>5.245</td>
        <td>economy_gdp_per_capita</td>
        <td>1.0278</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Bhutan</td>
        <td>Southern Asia</td>
        <td>84</td>
        <td>5.196</td>
        <td>economy_gdp_per_capita</td>
        <td>0.8527</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Kyrgyzstan</td>
        <td>Central and Eastern Europe</td>
        <td>85</td>
        <td>5.185</td>
        <td>economy_gdp_per_capita</td>
        <td>0.56044</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Serbia</td>
        <td>Central and Eastern Europe</td>
        <td>86</td>
        <td>5.177</td>
        <td>economy_gdp_per_capita</td>
        <td>1.03437</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Bosnia and Herzegovina</td>
        <td>Central and Eastern Europe</td>
        <td>87</td>
        <td>5.163</td>
        <td>economy_gdp_per_capita</td>
        <td>0.93383</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Montenegro</td>
        <td>Central and Eastern Europe</td>
        <td>88</td>
        <td>5.161</td>
        <td>economy_gdp_per_capita</td>
        <td>1.07838</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Dominican Republic</td>
        <td>Latin America and Caribbean</td>
        <td>89</td>
        <td>5.155</td>
        <td>economy_gdp_per_capita</td>
        <td>1.02787</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Morocco</td>
        <td>Middle East and Northern Africa</td>
        <td>90</td>
        <td>5.151</td>
        <td>economy_gdp_per_capita</td>
        <td>0.84058</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Hungary</td>
        <td>Central and Eastern Europe</td>
        <td>91</td>
        <td>5.145</td>
        <td>economy_gdp_per_capita</td>
        <td>1.24142</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Pakistan</td>
        <td>Southern Asia</td>
        <td>92</td>
        <td>5.132</td>
        <td>economy_gdp_per_capita</td>
        <td>0.68816</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Lebanon</td>
        <td>Middle East and Northern Africa</td>
        <td>93</td>
        <td>5.129</td>
        <td>economy_gdp_per_capita</td>
        <td>1.12268</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Portugal</td>
        <td>Western Europe</td>
        <td>94</td>
        <td>5.123</td>
        <td>economy_gdp_per_capita</td>
        <td>1.27607</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Macedonia</td>
        <td>Central and Eastern Europe</td>
        <td>95</td>
        <td>5.121</td>
        <td>economy_gdp_per_capita</td>
        <td>1.0193</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Vietnam</td>
        <td>Southeastern Asia</td>
        <td>96</td>
        <td>5.061</td>
        <td>economy_gdp_per_capita</td>
        <td>0.74037</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Somaliland Region</td>
        <td>Sub-Saharan Africa</td>
        <td>97</td>
        <td>5.057</td>
        <td>economy_gdp_per_capita</td>
        <td>0.25558</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Tunisia</td>
        <td>Middle East and Northern Africa</td>
        <td>98</td>
        <td>5.045</td>
        <td>economy_gdp_per_capita</td>
        <td>0.97724</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Greece</td>
        <td>Western Europe</td>
        <td>99</td>
        <td>5.033</td>
        <td>economy_gdp_per_capita</td>
        <td>1.24886</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Tajikistan</td>
        <td>Central and Eastern Europe</td>
        <td>100</td>
        <td>4.996</td>
        <td>economy_gdp_per_capita</td>
        <td>0.48835</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Mongolia</td>
        <td>Eastern Asia</td>
        <td>101</td>
        <td>4.907</td>
        <td>economy_gdp_per_capita</td>
        <td>0.98853</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Laos</td>
        <td>Southeastern Asia</td>
        <td>102</td>
        <td>4.876</td>
        <td>economy_gdp_per_capita</td>
        <td>0.68042</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Nigeria</td>
        <td>Sub-Saharan Africa</td>
        <td>103</td>
        <td>4.875</td>
        <td>economy_gdp_per_capita</td>
        <td>0.75216</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Honduras</td>
        <td>Latin America and Caribbean</td>
        <td>104</td>
        <td>4.871</td>
        <td>economy_gdp_per_capita</td>
        <td>0.69429</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Iran</td>
        <td>Middle East and Northern Africa</td>
        <td>105</td>
        <td>4.813</td>
        <td>economy_gdp_per_capita</td>
        <td>1.11758</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Zambia</td>
        <td>Sub-Saharan Africa</td>
        <td>106</td>
        <td>4.795</td>
        <td>economy_gdp_per_capita</td>
        <td>0.61202</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Nepal</td>
        <td>Southern Asia</td>
        <td>107</td>
        <td>4.793</td>
        <td>economy_gdp_per_capita</td>
        <td>0.44626</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Palestinian Territories</td>
        <td>Middle East and Northern Africa</td>
        <td>108</td>
        <td>4.754</td>
        <td>economy_gdp_per_capita</td>
        <td>0.67024</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Albania</td>
        <td>Central and Eastern Europe</td>
        <td>109</td>
        <td>4.655</td>
        <td>economy_gdp_per_capita</td>
        <td>0.9553</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Bangladesh</td>
        <td>Southern Asia</td>
        <td>110</td>
        <td>4.643</td>
        <td>economy_gdp_per_capita</td>
        <td>0.54177</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Sierra Leone</td>
        <td>Sub-Saharan Africa</td>
        <td>111</td>
        <td>4.635</td>
        <td>economy_gdp_per_capita</td>
        <td>0.36485</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Iraq</td>
        <td>Middle East and Northern Africa</td>
        <td>112</td>
        <td>4.575</td>
        <td>economy_gdp_per_capita</td>
        <td>1.07474</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Namibia</td>
        <td>Sub-Saharan Africa</td>
        <td>113</td>
        <td>4.574</td>
        <td>economy_gdp_per_capita</td>
        <td>0.93287</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Cameroon</td>
        <td>Sub-Saharan Africa</td>
        <td>114</td>
        <td>4.513</td>
        <td>economy_gdp_per_capita</td>
        <td>0.52497</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Ethiopia</td>
        <td>Sub-Saharan Africa</td>
        <td>115</td>
        <td>4.508</td>
        <td>economy_gdp_per_capita</td>
        <td>0.29283</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>South Africa</td>
        <td>Sub-Saharan Africa</td>
        <td>116</td>
        <td>4.459</td>
        <td>economy_gdp_per_capita</td>
        <td>1.02416</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Sri Lanka</td>
        <td>Southern Asia</td>
        <td>117</td>
        <td>4.415</td>
        <td>economy_gdp_per_capita</td>
        <td>0.97318</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>India</td>
        <td>Southern Asia</td>
        <td>118</td>
        <td>4.404</td>
        <td>economy_gdp_per_capita</td>
        <td>0.74036</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Myanmar</td>
        <td>Southeastern Asia</td>
        <td>119</td>
        <td>4.395</td>
        <td>economy_gdp_per_capita</td>
        <td>0.34112</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Egypt</td>
        <td>Middle East and Northern Africa</td>
        <td>120</td>
        <td>4.362</td>
        <td>economy_gdp_per_capita</td>
        <td>0.95395</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Armenia</td>
        <td>Central and Eastern Europe</td>
        <td>121</td>
        <td>4.36</td>
        <td>economy_gdp_per_capita</td>
        <td>0.86086</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Kenya</td>
        <td>Sub-Saharan Africa</td>
        <td>122</td>
        <td>4.356</td>
        <td>economy_gdp_per_capita</td>
        <td>0.52267</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Ukraine</td>
        <td>Central and Eastern Europe</td>
        <td>123</td>
        <td>4.324</td>
        <td>economy_gdp_per_capita</td>
        <td>0.87287</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Ghana</td>
        <td>Sub-Saharan Africa</td>
        <td>124</td>
        <td>4.276</td>
        <td>economy_gdp_per_capita</td>
        <td>0.63107</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Congo (Kinshasa)</td>
        <td>Sub-Saharan Africa</td>
        <td>125</td>
        <td>4.272</td>
        <td>economy_gdp_per_capita</td>
        <td>0.05661</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Georgia</td>
        <td>Central and Eastern Europe</td>
        <td>126</td>
        <td>4.252</td>
        <td>economy_gdp_per_capita</td>
        <td>0.83792</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Congo (Brazzaville)</td>
        <td>Sub-Saharan Africa</td>
        <td>127</td>
        <td>4.236</td>
        <td>economy_gdp_per_capita</td>
        <td>0.77109</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Senegal</td>
        <td>Sub-Saharan Africa</td>
        <td>128</td>
        <td>4.219</td>
        <td>economy_gdp_per_capita</td>
        <td>0.44314</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Bulgaria</td>
        <td>Central and Eastern Europe</td>
        <td>129</td>
        <td>4.217</td>
        <td>economy_gdp_per_capita</td>
        <td>1.11306</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Mauritania</td>
        <td>Sub-Saharan Africa</td>
        <td>130</td>
        <td>4.201</td>
        <td>economy_gdp_per_capita</td>
        <td>0.61391</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Zimbabwe</td>
        <td>Sub-Saharan Africa</td>
        <td>131</td>
        <td>4.193</td>
        <td>economy_gdp_per_capita</td>
        <td>0.35041</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Malawi</td>
        <td>Sub-Saharan Africa</td>
        <td>132</td>
        <td>4.156</td>
        <td>economy_gdp_per_capita</td>
        <td>0.08709</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Sudan</td>
        <td>Sub-Saharan Africa</td>
        <td>133</td>
        <td>4.139</td>
        <td>economy_gdp_per_capita</td>
        <td>0.63069</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Gabon</td>
        <td>Sub-Saharan Africa</td>
        <td>134</td>
        <td>4.121</td>
        <td>economy_gdp_per_capita</td>
        <td>1.15851</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Mali</td>
        <td>Sub-Saharan Africa</td>
        <td>135</td>
        <td>4.073</td>
        <td>economy_gdp_per_capita</td>
        <td>0.31292</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Haiti</td>
        <td>Latin America and Caribbean</td>
        <td>136</td>
        <td>4.028</td>
        <td>economy_gdp_per_capita</td>
        <td>0.34097</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Botswana</td>
        <td>Sub-Saharan Africa</td>
        <td>137</td>
        <td>3.974</td>
        <td>economy_gdp_per_capita</td>
        <td>1.09426</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Comoros</td>
        <td>Sub-Saharan Africa</td>
        <td>138</td>
        <td>3.956</td>
        <td>economy_gdp_per_capita</td>
        <td>0.27509</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Ivory Coast</td>
        <td>Sub-Saharan Africa</td>
        <td>139</td>
        <td>3.916</td>
        <td>economy_gdp_per_capita</td>
        <td>0.55507</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Cambodia</td>
        <td>Southeastern Asia</td>
        <td>140</td>
        <td>3.907</td>
        <td>economy_gdp_per_capita</td>
        <td>0.55604</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Angola</td>
        <td>Sub-Saharan Africa</td>
        <td>141</td>
        <td>3.866</td>
        <td>economy_gdp_per_capita</td>
        <td>0.84731</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Niger</td>
        <td>Sub-Saharan Africa</td>
        <td>142</td>
        <td>3.856</td>
        <td>economy_gdp_per_capita</td>
        <td>0.1327</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>South Sudan</td>
        <td>Sub-Saharan Africa</td>
        <td>143</td>
        <td>3.832</td>
        <td>economy_gdp_per_capita</td>
        <td>0.39394</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Chad</td>
        <td>Sub-Saharan Africa</td>
        <td>144</td>
        <td>3.763</td>
        <td>economy_gdp_per_capita</td>
        <td>0.42214</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Burkina Faso</td>
        <td>Sub-Saharan Africa</td>
        <td>145</td>
        <td>3.739</td>
        <td>economy_gdp_per_capita</td>
        <td>0.31995</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Uganda</td>
        <td>Sub-Saharan Africa</td>
        <td>145</td>
        <td>3.739</td>
        <td>economy_gdp_per_capita</td>
        <td>0.34719</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Yemen</td>
        <td>Middle East and Northern Africa</td>
        <td>147</td>
        <td>3.724</td>
        <td>economy_gdp_per_capita</td>
        <td>0.57939</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Madagascar</td>
        <td>Sub-Saharan Africa</td>
        <td>148</td>
        <td>3.695</td>
        <td>economy_gdp_per_capita</td>
        <td>0.27954</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Tanzania</td>
        <td>Sub-Saharan Africa</td>
        <td>149</td>
        <td>3.666</td>
        <td>economy_gdp_per_capita</td>
        <td>0.47155</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Liberia</td>
        <td>Sub-Saharan Africa</td>
        <td>150</td>
        <td>3.622</td>
        <td>economy_gdp_per_capita</td>
        <td>0.10706</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Guinea</td>
        <td>Sub-Saharan Africa</td>
        <td>151</td>
        <td>3.607</td>
        <td>economy_gdp_per_capita</td>
        <td>0.22415</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Rwanda</td>
        <td>Sub-Saharan Africa</td>
        <td>152</td>
        <td>3.515</td>
        <td>economy_gdp_per_capita</td>
        <td>0.32846</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Benin</td>
        <td>Sub-Saharan Africa</td>
        <td>153</td>
        <td>3.484</td>
        <td>economy_gdp_per_capita</td>
        <td>0.39499</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Afghanistan</td>
        <td>Southern Asia</td>
        <td>154</td>
        <td>3.36</td>
        <td>economy_gdp_per_capita</td>
        <td>0.38227</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Togo</td>
        <td>Sub-Saharan Africa</td>
        <td>155</td>
        <td>3.303</td>
        <td>economy_gdp_per_capita</td>
        <td>0.28123</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Syria</td>
        <td>Middle East and Northern Africa</td>
        <td>156</td>
        <td>3.069</td>
        <td>economy_gdp_per_capita</td>
        <td>0.74719</td>
    </tr>
    <tr>
        <td>2016</td>
        <td>Burundi</td>
        <td>Sub-Saharan Africa</td>
        <td>157</td>
        <td>2.905</td>
        <td>economy_gdp_per_capita</td>
        <td>0.06831</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Norway</td>
        <td>Western Europe</td>
        <td>1</td>
        <td>7.53700017929077</td>
        <td>economy_gdp_per_capita</td>
        <td>1.61646318435669</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Denmark</td>
        <td>Western Europe</td>
        <td>2</td>
        <td>7.52199983596802</td>
        <td>economy_gdp_per_capita</td>
        <td>1.48238301277161</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Iceland</td>
        <td>Western Europe</td>
        <td>3</td>
        <td>7.50400018692017</td>
        <td>economy_gdp_per_capita</td>
        <td>1.480633020401</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Switzerland</td>
        <td>Western Europe</td>
        <td>4</td>
        <td>7.49399995803833</td>
        <td>economy_gdp_per_capita</td>
        <td>1.56497955322266</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Finland</td>
        <td>Western Europe</td>
        <td>5</td>
        <td>7.4689998626709</td>
        <td>economy_gdp_per_capita</td>
        <td>1.44357192516327</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Netherlands</td>
        <td>Western Europe</td>
        <td>6</td>
        <td>7.3769998550415</td>
        <td>economy_gdp_per_capita</td>
        <td>1.50394463539124</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Canada</td>
        <td>North America</td>
        <td>7</td>
        <td>7.31599998474121</td>
        <td>economy_gdp_per_capita</td>
        <td>1.47920441627502</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>New Zealand</td>
        <td>Australia and New Zealand</td>
        <td>8</td>
        <td>7.31400012969971</td>
        <td>economy_gdp_per_capita</td>
        <td>1.40570604801178</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Sweden</td>
        <td>Western Europe</td>
        <td>9</td>
        <td>7.28399991989136</td>
        <td>economy_gdp_per_capita</td>
        <td>1.49438726902008</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Australia</td>
        <td>Australia and New Zealand</td>
        <td>10</td>
        <td>7.28399991989136</td>
        <td>economy_gdp_per_capita</td>
        <td>1.484414935112</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Israel</td>
        <td>Middle East and Northern Africa</td>
        <td>11</td>
        <td>7.21299982070923</td>
        <td>economy_gdp_per_capita</td>
        <td>1.37538242340088</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Costa Rica</td>
        <td>Latin America and Caribbean</td>
        <td>12</td>
        <td>7.0789999961853</td>
        <td>economy_gdp_per_capita</td>
        <td>1.10970628261566</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Austria</td>
        <td>Western Europe</td>
        <td>13</td>
        <td>7.00600004196167</td>
        <td>economy_gdp_per_capita</td>
        <td>1.48709726333618</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>United States</td>
        <td>North America</td>
        <td>14</td>
        <td>6.99300003051758</td>
        <td>economy_gdp_per_capita</td>
        <td>1.54625928401947</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Ireland</td>
        <td>Western Europe</td>
        <td>15</td>
        <td>6.97700023651123</td>
        <td>economy_gdp_per_capita</td>
        <td>1.53570663928986</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Germany</td>
        <td>Western Europe</td>
        <td>16</td>
        <td>6.95100021362305</td>
        <td>economy_gdp_per_capita</td>
        <td>1.48792338371277</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Belgium</td>
        <td>Western Europe</td>
        <td>17</td>
        <td>6.89099979400635</td>
        <td>economy_gdp_per_capita</td>
        <td>1.46378076076508</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Luxembourg</td>
        <td>Western Europe</td>
        <td>18</td>
        <td>6.86299991607666</td>
        <td>economy_gdp_per_capita</td>
        <td>1.74194359779358</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>United Kingdom</td>
        <td>Western Europe</td>
        <td>19</td>
        <td>6.71400022506714</td>
        <td>economy_gdp_per_capita</td>
        <td>1.44163393974304</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Chile</td>
        <td>Latin America and Caribbean</td>
        <td>20</td>
        <td>6.65199995040894</td>
        <td>economy_gdp_per_capita</td>
        <td>1.25278460979462</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>United Arab Emirates</td>
        <td>Middle East and Northern Africa</td>
        <td>21</td>
        <td>6.64799976348877</td>
        <td>economy_gdp_per_capita</td>
        <td>1.62634336948395</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Brazil</td>
        <td>Latin America and Caribbean</td>
        <td>22</td>
        <td>6.63500022888184</td>
        <td>economy_gdp_per_capita</td>
        <td>1.10735321044922</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Czech Republic</td>
        <td>Central and Eastern Europe</td>
        <td>23</td>
        <td>6.60900020599365</td>
        <td>economy_gdp_per_capita</td>
        <td>1.35268235206604</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Argentina</td>
        <td>Latin America and Caribbean</td>
        <td>24</td>
        <td>6.59899997711182</td>
        <td>economy_gdp_per_capita</td>
        <td>1.18529546260834</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Mexico</td>
        <td>Latin America and Caribbean</td>
        <td>25</td>
        <td>6.57800006866455</td>
        <td>economy_gdp_per_capita</td>
        <td>1.15318381786346</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Singapore</td>
        <td>Southeastern Asia</td>
        <td>26</td>
        <td>6.57200002670288</td>
        <td>economy_gdp_per_capita</td>
        <td>1.69227766990662</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Malta</td>
        <td>Western Europe</td>
        <td>27</td>
        <td>6.52699995040894</td>
        <td>economy_gdp_per_capita</td>
        <td>1.34327983856201</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Uruguay</td>
        <td>Latin America and Caribbean</td>
        <td>28</td>
        <td>6.4539999961853</td>
        <td>economy_gdp_per_capita</td>
        <td>1.21755969524384</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Guatemala</td>
        <td>Latin America and Caribbean</td>
        <td>29</td>
        <td>6.4539999961853</td>
        <td>economy_gdp_per_capita</td>
        <td>0.872001945972443</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Panama</td>
        <td>Latin America and Caribbean</td>
        <td>30</td>
        <td>6.4520001411438</td>
        <td>economy_gdp_per_capita</td>
        <td>1.23374843597412</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>France</td>
        <td>Western Europe</td>
        <td>31</td>
        <td>6.44199991226196</td>
        <td>economy_gdp_per_capita</td>
        <td>1.43092346191406</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Thailand</td>
        <td>Southeastern Asia</td>
        <td>32</td>
        <td>6.42399978637695</td>
        <td>economy_gdp_per_capita</td>
        <td>1.12786877155304</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Taiwan Province of China</td>
        <td>None</td>
        <td>33</td>
        <td>6.42199993133545</td>
        <td>economy_gdp_per_capita</td>
        <td>1.43362653255463</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Spain</td>
        <td>Western Europe</td>
        <td>34</td>
        <td>6.40299987792969</td>
        <td>economy_gdp_per_capita</td>
        <td>1.38439786434174</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Qatar</td>
        <td>Middle East and Northern Africa</td>
        <td>35</td>
        <td>6.375</td>
        <td>economy_gdp_per_capita</td>
        <td>1.87076568603516</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Colombia</td>
        <td>Latin America and Caribbean</td>
        <td>36</td>
        <td>6.35699987411499</td>
        <td>economy_gdp_per_capita</td>
        <td>1.07062232494354</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Saudi Arabia</td>
        <td>Middle East and Northern Africa</td>
        <td>37</td>
        <td>6.3439998626709</td>
        <td>economy_gdp_per_capita</td>
        <td>1.53062355518341</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Trinidad and Tobago</td>
        <td>Latin America and Caribbean</td>
        <td>38</td>
        <td>6.16800022125244</td>
        <td>economy_gdp_per_capita</td>
        <td>1.36135590076447</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Kuwait</td>
        <td>Middle East and Northern Africa</td>
        <td>39</td>
        <td>6.10500001907349</td>
        <td>economy_gdp_per_capita</td>
        <td>1.63295245170593</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Slovakia</td>
        <td>Central and Eastern Europe</td>
        <td>40</td>
        <td>6.09800004959106</td>
        <td>economy_gdp_per_capita</td>
        <td>1.32539355754852</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Bahrain</td>
        <td>Middle East and Northern Africa</td>
        <td>41</td>
        <td>6.08699989318848</td>
        <td>economy_gdp_per_capita</td>
        <td>1.48841226100922</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Malaysia</td>
        <td>Southeastern Asia</td>
        <td>42</td>
        <td>6.08400011062622</td>
        <td>economy_gdp_per_capita</td>
        <td>1.29121541976929</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Nicaragua</td>
        <td>Latin America and Caribbean</td>
        <td>43</td>
        <td>6.07100009918213</td>
        <td>economy_gdp_per_capita</td>
        <td>0.737299203872681</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Ecuador</td>
        <td>Latin America and Caribbean</td>
        <td>44</td>
        <td>6.00799989700317</td>
        <td>economy_gdp_per_capita</td>
        <td>1.00082039833069</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>El Salvador</td>
        <td>Latin America and Caribbean</td>
        <td>45</td>
        <td>6.00299978256226</td>
        <td>economy_gdp_per_capita</td>
        <td>0.909784495830536</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Poland</td>
        <td>Central and Eastern Europe</td>
        <td>46</td>
        <td>5.97300004959106</td>
        <td>economy_gdp_per_capita</td>
        <td>1.29178786277771</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Uzbekistan</td>
        <td>Central and Eastern Europe</td>
        <td>47</td>
        <td>5.97100019454956</td>
        <td>economy_gdp_per_capita</td>
        <td>0.786441087722778</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Italy</td>
        <td>Western Europe</td>
        <td>48</td>
        <td>5.96400022506714</td>
        <td>economy_gdp_per_capita</td>
        <td>1.39506661891937</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Russia</td>
        <td>Central and Eastern Europe</td>
        <td>49</td>
        <td>5.96299982070923</td>
        <td>economy_gdp_per_capita</td>
        <td>1.28177809715271</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Belize</td>
        <td>Latin America and Caribbean</td>
        <td>50</td>
        <td>5.95599985122681</td>
        <td>economy_gdp_per_capita</td>
        <td>0.907975316047668</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Japan</td>
        <td>Eastern Asia</td>
        <td>51</td>
        <td>5.92000007629395</td>
        <td>economy_gdp_per_capita</td>
        <td>1.41691517829895</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Lithuania</td>
        <td>Central and Eastern Europe</td>
        <td>52</td>
        <td>5.90199995040894</td>
        <td>economy_gdp_per_capita</td>
        <td>1.31458234786987</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Algeria</td>
        <td>Middle East and Northern Africa</td>
        <td>53</td>
        <td>5.87200021743774</td>
        <td>economy_gdp_per_capita</td>
        <td>1.09186446666718</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Latvia</td>
        <td>Central and Eastern Europe</td>
        <td>54</td>
        <td>5.84999990463257</td>
        <td>economy_gdp_per_capita</td>
        <td>1.26074862480164</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>South Korea</td>
        <td>Eastern Asia</td>
        <td>55</td>
        <td>5.83799982070923</td>
        <td>economy_gdp_per_capita</td>
        <td>1.40167844295502</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Moldova</td>
        <td>Central and Eastern Europe</td>
        <td>56</td>
        <td>5.83799982070923</td>
        <td>economy_gdp_per_capita</td>
        <td>0.728870630264282</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Romania</td>
        <td>Central and Eastern Europe</td>
        <td>57</td>
        <td>5.82499980926514</td>
        <td>economy_gdp_per_capita</td>
        <td>1.21768391132355</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Bolivia</td>
        <td>Latin America and Caribbean</td>
        <td>58</td>
        <td>5.82299995422363</td>
        <td>economy_gdp_per_capita</td>
        <td>0.833756566047668</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Turkmenistan</td>
        <td>Central and Eastern Europe</td>
        <td>59</td>
        <td>5.82200002670288</td>
        <td>economy_gdp_per_capita</td>
        <td>1.13077676296234</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Kazakhstan</td>
        <td>Central and Eastern Europe</td>
        <td>60</td>
        <td>5.81899976730347</td>
        <td>economy_gdp_per_capita</td>
        <td>1.28455626964569</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>North Cyprus</td>
        <td>Western Europe</td>
        <td>61</td>
        <td>5.80999994277954</td>
        <td>economy_gdp_per_capita</td>
        <td>1.3469113111496</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Slovenia</td>
        <td>Central and Eastern Europe</td>
        <td>62</td>
        <td>5.75799989700317</td>
        <td>economy_gdp_per_capita</td>
        <td>1.3412059545517</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Peru</td>
        <td>Latin America and Caribbean</td>
        <td>63</td>
        <td>5.71500015258789</td>
        <td>economy_gdp_per_capita</td>
        <td>1.03522527217865</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Mauritius</td>
        <td>Sub-Saharan Africa</td>
        <td>64</td>
        <td>5.62900018692017</td>
        <td>economy_gdp_per_capita</td>
        <td>1.18939554691315</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Cyprus</td>
        <td>Western Europe</td>
        <td>65</td>
        <td>5.62099981307983</td>
        <td>economy_gdp_per_capita</td>
        <td>1.35593807697296</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Estonia</td>
        <td>Central and Eastern Europe</td>
        <td>66</td>
        <td>5.61100006103516</td>
        <td>economy_gdp_per_capita</td>
        <td>1.32087934017181</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Belarus</td>
        <td>Central and Eastern Europe</td>
        <td>67</td>
        <td>5.56899976730347</td>
        <td>economy_gdp_per_capita</td>
        <td>1.15655755996704</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Libya</td>
        <td>Middle East and Northern Africa</td>
        <td>68</td>
        <td>5.52500009536743</td>
        <td>economy_gdp_per_capita</td>
        <td>1.10180306434631</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Turkey</td>
        <td>Middle East and Northern Africa</td>
        <td>69</td>
        <td>5.5</td>
        <td>economy_gdp_per_capita</td>
        <td>1.19827437400818</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Paraguay</td>
        <td>Latin America and Caribbean</td>
        <td>70</td>
        <td>5.49300003051758</td>
        <td>economy_gdp_per_capita</td>
        <td>0.932537317276001</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Hong Kong S.A.R., China</td>
        <td>None</td>
        <td>71</td>
        <td>5.47200012207031</td>
        <td>economy_gdp_per_capita</td>
        <td>1.55167484283447</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Philippines</td>
        <td>Southeastern Asia</td>
        <td>72</td>
        <td>5.42999982833862</td>
        <td>economy_gdp_per_capita</td>
        <td>0.85769921541214</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Serbia</td>
        <td>Central and Eastern Europe</td>
        <td>73</td>
        <td>5.39499998092651</td>
        <td>economy_gdp_per_capita</td>
        <td>1.06931757926941</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Jordan</td>
        <td>Middle East and Northern Africa</td>
        <td>74</td>
        <td>5.33599996566772</td>
        <td>economy_gdp_per_capita</td>
        <td>0.991012394428253</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Hungary</td>
        <td>Central and Eastern Europe</td>
        <td>75</td>
        <td>5.32399988174438</td>
        <td>economy_gdp_per_capita</td>
        <td>1.2860119342804</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Jamaica</td>
        <td>Latin America and Caribbean</td>
        <td>76</td>
        <td>5.31099987030029</td>
        <td>economy_gdp_per_capita</td>
        <td>0.925579309463501</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Croatia</td>
        <td>Central and Eastern Europe</td>
        <td>77</td>
        <td>5.29300022125244</td>
        <td>economy_gdp_per_capita</td>
        <td>1.22255623340607</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Kosovo</td>
        <td>Central and Eastern Europe</td>
        <td>78</td>
        <td>5.27899980545044</td>
        <td>economy_gdp_per_capita</td>
        <td>0.951484382152557</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>China</td>
        <td>Eastern Asia</td>
        <td>79</td>
        <td>5.27299976348877</td>
        <td>economy_gdp_per_capita</td>
        <td>1.08116579055786</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Pakistan</td>
        <td>Southern Asia</td>
        <td>80</td>
        <td>5.26900005340576</td>
        <td>economy_gdp_per_capita</td>
        <td>0.72688353061676</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Indonesia</td>
        <td>Southeastern Asia</td>
        <td>81</td>
        <td>5.26200008392334</td>
        <td>economy_gdp_per_capita</td>
        <td>0.995538592338562</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Venezuela</td>
        <td>Latin America and Caribbean</td>
        <td>82</td>
        <td>5.25</td>
        <td>economy_gdp_per_capita</td>
        <td>1.12843120098114</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Montenegro</td>
        <td>Central and Eastern Europe</td>
        <td>83</td>
        <td>5.23699998855591</td>
        <td>economy_gdp_per_capita</td>
        <td>1.12112903594971</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Morocco</td>
        <td>Middle East and Northern Africa</td>
        <td>84</td>
        <td>5.2350001335144</td>
        <td>economy_gdp_per_capita</td>
        <td>0.878114581108093</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Azerbaijan</td>
        <td>Central and Eastern Europe</td>
        <td>85</td>
        <td>5.23400020599365</td>
        <td>economy_gdp_per_capita</td>
        <td>1.15360176563263</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Dominican Republic</td>
        <td>Latin America and Caribbean</td>
        <td>86</td>
        <td>5.23000001907349</td>
        <td>economy_gdp_per_capita</td>
        <td>1.07937383651733</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Greece</td>
        <td>Western Europe</td>
        <td>87</td>
        <td>5.22700023651123</td>
        <td>economy_gdp_per_capita</td>
        <td>1.28948748111725</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Lebanon</td>
        <td>Middle East and Northern Africa</td>
        <td>88</td>
        <td>5.22499990463257</td>
        <td>economy_gdp_per_capita</td>
        <td>1.07498753070831</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Portugal</td>
        <td>Western Europe</td>
        <td>89</td>
        <td>5.19500017166138</td>
        <td>economy_gdp_per_capita</td>
        <td>1.3151752948761</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Bosnia and Herzegovina</td>
        <td>Central and Eastern Europe</td>
        <td>90</td>
        <td>5.18200016021729</td>
        <td>economy_gdp_per_capita</td>
        <td>0.982409417629242</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Honduras</td>
        <td>Latin America and Caribbean</td>
        <td>91</td>
        <td>5.18100023269653</td>
        <td>economy_gdp_per_capita</td>
        <td>0.730573117733002</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Macedonia</td>
        <td>Central and Eastern Europe</td>
        <td>92</td>
        <td>5.17500019073486</td>
        <td>economy_gdp_per_capita</td>
        <td>1.06457793712616</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Somalia</td>
        <td>Sub-Saharan Africa</td>
        <td>93</td>
        <td>5.15100002288818</td>
        <td>economy_gdp_per_capita</td>
        <td>0.0226431842893362</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Vietnam</td>
        <td>Southeastern Asia</td>
        <td>94</td>
        <td>5.07399988174438</td>
        <td>economy_gdp_per_capita</td>
        <td>0.788547575473785</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Nigeria</td>
        <td>Sub-Saharan Africa</td>
        <td>95</td>
        <td>5.07399988174438</td>
        <td>economy_gdp_per_capita</td>
        <td>0.783756256103516</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Tajikistan</td>
        <td>Central and Eastern Europe</td>
        <td>96</td>
        <td>5.04099988937378</td>
        <td>economy_gdp_per_capita</td>
        <td>0.524713635444641</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Bhutan</td>
        <td>Southern Asia</td>
        <td>97</td>
        <td>5.01100015640259</td>
        <td>economy_gdp_per_capita</td>
        <td>0.885416388511658</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Kyrgyzstan</td>
        <td>Central and Eastern Europe</td>
        <td>98</td>
        <td>5.00400018692017</td>
        <td>economy_gdp_per_capita</td>
        <td>0.596220076084137</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Nepal</td>
        <td>Southern Asia</td>
        <td>99</td>
        <td>4.96199989318848</td>
        <td>economy_gdp_per_capita</td>
        <td>0.479820191860199</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Mongolia</td>
        <td>Eastern Asia</td>
        <td>100</td>
        <td>4.95499992370605</td>
        <td>economy_gdp_per_capita</td>
        <td>1.02723586559296</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>South Africa</td>
        <td>Sub-Saharan Africa</td>
        <td>101</td>
        <td>4.8289999961853</td>
        <td>economy_gdp_per_capita</td>
        <td>1.05469870567322</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Tunisia</td>
        <td>Middle East and Northern Africa</td>
        <td>102</td>
        <td>4.80499982833862</td>
        <td>economy_gdp_per_capita</td>
        <td>1.00726580619812</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Palestinian Territories</td>
        <td>Middle East and Northern Africa</td>
        <td>103</td>
        <td>4.77500009536743</td>
        <td>economy_gdp_per_capita</td>
        <td>0.716249227523804</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Egypt</td>
        <td>Middle East and Northern Africa</td>
        <td>104</td>
        <td>4.7350001335144</td>
        <td>economy_gdp_per_capita</td>
        <td>0.989701807498932</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Bulgaria</td>
        <td>Central and Eastern Europe</td>
        <td>105</td>
        <td>4.71400022506714</td>
        <td>economy_gdp_per_capita</td>
        <td>1.1614590883255</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Sierra Leone</td>
        <td>Sub-Saharan Africa</td>
        <td>106</td>
        <td>4.70900011062622</td>
        <td>economy_gdp_per_capita</td>
        <td>0.36842092871666</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Cameroon</td>
        <td>Sub-Saharan Africa</td>
        <td>107</td>
        <td>4.69500017166138</td>
        <td>economy_gdp_per_capita</td>
        <td>0.564305365085602</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Iran</td>
        <td>Middle East and Northern Africa</td>
        <td>108</td>
        <td>4.69199991226196</td>
        <td>economy_gdp_per_capita</td>
        <td>1.15687310695648</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Albania</td>
        <td>Central and Eastern Europe</td>
        <td>109</td>
        <td>4.64400005340576</td>
        <td>economy_gdp_per_capita</td>
        <td>0.996192753314972</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Bangladesh</td>
        <td>Southern Asia</td>
        <td>110</td>
        <td>4.60799980163574</td>
        <td>economy_gdp_per_capita</td>
        <td>0.586682975292206</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Namibia</td>
        <td>Sub-Saharan Africa</td>
        <td>111</td>
        <td>4.57399988174438</td>
        <td>economy_gdp_per_capita</td>
        <td>0.964434325695038</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Kenya</td>
        <td>Sub-Saharan Africa</td>
        <td>112</td>
        <td>4.55299997329712</td>
        <td>economy_gdp_per_capita</td>
        <td>0.560479462146759</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Mozambique</td>
        <td>Sub-Saharan Africa</td>
        <td>113</td>
        <td>4.55000019073486</td>
        <td>economy_gdp_per_capita</td>
        <td>0.234305649995804</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Myanmar</td>
        <td>Southeastern Asia</td>
        <td>114</td>
        <td>4.54500007629395</td>
        <td>economy_gdp_per_capita</td>
        <td>0.367110550403595</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Senegal</td>
        <td>Sub-Saharan Africa</td>
        <td>115</td>
        <td>4.53499984741211</td>
        <td>economy_gdp_per_capita</td>
        <td>0.479309022426605</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Zambia</td>
        <td>Sub-Saharan Africa</td>
        <td>116</td>
        <td>4.51399993896484</td>
        <td>economy_gdp_per_capita</td>
        <td>0.636406779289246</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Iraq</td>
        <td>Middle East and Northern Africa</td>
        <td>117</td>
        <td>4.49700021743774</td>
        <td>economy_gdp_per_capita</td>
        <td>1.10271048545837</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Gabon</td>
        <td>Sub-Saharan Africa</td>
        <td>118</td>
        <td>4.46500015258789</td>
        <td>economy_gdp_per_capita</td>
        <td>1.1982102394104</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Ethiopia</td>
        <td>Sub-Saharan Africa</td>
        <td>119</td>
        <td>4.46000003814697</td>
        <td>economy_gdp_per_capita</td>
        <td>0.339233845472336</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Sri Lanka</td>
        <td>Southern Asia</td>
        <td>120</td>
        <td>4.44000005722046</td>
        <td>economy_gdp_per_capita</td>
        <td>1.00985014438629</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Armenia</td>
        <td>Central and Eastern Europe</td>
        <td>121</td>
        <td>4.37599992752075</td>
        <td>economy_gdp_per_capita</td>
        <td>0.900596737861633</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>India</td>
        <td>Southern Asia</td>
        <td>122</td>
        <td>4.31500005722046</td>
        <td>economy_gdp_per_capita</td>
        <td>0.792221248149872</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Mauritania</td>
        <td>Sub-Saharan Africa</td>
        <td>123</td>
        <td>4.29199981689453</td>
        <td>economy_gdp_per_capita</td>
        <td>0.648457288742065</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Congo (Brazzaville)</td>
        <td>Sub-Saharan Africa</td>
        <td>124</td>
        <td>4.29099988937378</td>
        <td>economy_gdp_per_capita</td>
        <td>0.808964252471924</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Georgia</td>
        <td>Central and Eastern Europe</td>
        <td>125</td>
        <td>4.28599977493286</td>
        <td>economy_gdp_per_capita</td>
        <td>0.950612664222717</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Congo (Kinshasa)</td>
        <td>Sub-Saharan Africa</td>
        <td>126</td>
        <td>4.28000020980835</td>
        <td>economy_gdp_per_capita</td>
        <td>0.0921023488044739</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Mali</td>
        <td>Sub-Saharan Africa</td>
        <td>127</td>
        <td>4.19000005722046</td>
        <td>economy_gdp_per_capita</td>
        <td>0.476180493831635</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Ivory Coast</td>
        <td>Sub-Saharan Africa</td>
        <td>128</td>
        <td>4.17999982833862</td>
        <td>economy_gdp_per_capita</td>
        <td>0.603048920631409</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Cambodia</td>
        <td>Southeastern Asia</td>
        <td>129</td>
        <td>4.16800022125244</td>
        <td>economy_gdp_per_capita</td>
        <td>0.601765096187592</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Sudan</td>
        <td>Sub-Saharan Africa</td>
        <td>130</td>
        <td>4.13899993896484</td>
        <td>economy_gdp_per_capita</td>
        <td>0.65951669216156</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Ghana</td>
        <td>Sub-Saharan Africa</td>
        <td>131</td>
        <td>4.11999988555908</td>
        <td>economy_gdp_per_capita</td>
        <td>0.667224824428558</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Ukraine</td>
        <td>Central and Eastern Europe</td>
        <td>132</td>
        <td>4.09600019454956</td>
        <td>economy_gdp_per_capita</td>
        <td>0.89465194940567</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Uganda</td>
        <td>Sub-Saharan Africa</td>
        <td>133</td>
        <td>4.08099985122681</td>
        <td>economy_gdp_per_capita</td>
        <td>0.381430715322495</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Burkina Faso</td>
        <td>Sub-Saharan Africa</td>
        <td>134</td>
        <td>4.03200006484985</td>
        <td>economy_gdp_per_capita</td>
        <td>0.3502277135849</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Niger</td>
        <td>Sub-Saharan Africa</td>
        <td>135</td>
        <td>4.02799987792969</td>
        <td>economy_gdp_per_capita</td>
        <td>0.161925330758095</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Malawi</td>
        <td>Sub-Saharan Africa</td>
        <td>136</td>
        <td>3.97000002861023</td>
        <td>economy_gdp_per_capita</td>
        <td>0.233442038297653</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Chad</td>
        <td>Sub-Saharan Africa</td>
        <td>137</td>
        <td>3.93600010871887</td>
        <td>economy_gdp_per_capita</td>
        <td>0.438012987375259</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Zimbabwe</td>
        <td>Sub-Saharan Africa</td>
        <td>138</td>
        <td>3.875</td>
        <td>economy_gdp_per_capita</td>
        <td>0.375846534967422</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Lesotho</td>
        <td>Sub-Saharan Africa</td>
        <td>139</td>
        <td>3.80800008773804</td>
        <td>economy_gdp_per_capita</td>
        <td>0.521021246910095</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Angola</td>
        <td>Sub-Saharan Africa</td>
        <td>140</td>
        <td>3.79500007629395</td>
        <td>economy_gdp_per_capita</td>
        <td>0.858428180217743</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Afghanistan</td>
        <td>Southern Asia</td>
        <td>141</td>
        <td>3.79399991035461</td>
        <td>economy_gdp_per_capita</td>
        <td>0.401477217674255</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Botswana</td>
        <td>Sub-Saharan Africa</td>
        <td>142</td>
        <td>3.76600003242493</td>
        <td>economy_gdp_per_capita</td>
        <td>1.12209415435791</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Benin</td>
        <td>Sub-Saharan Africa</td>
        <td>143</td>
        <td>3.65700006484985</td>
        <td>economy_gdp_per_capita</td>
        <td>0.431085407733917</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Madagascar</td>
        <td>Sub-Saharan Africa</td>
        <td>144</td>
        <td>3.64400005340576</td>
        <td>economy_gdp_per_capita</td>
        <td>0.305808693170547</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Haiti</td>
        <td>Latin America and Caribbean</td>
        <td>145</td>
        <td>3.6029999256134</td>
        <td>economy_gdp_per_capita</td>
        <td>0.368610262870789</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Yemen</td>
        <td>Middle East and Northern Africa</td>
        <td>146</td>
        <td>3.59299993515015</td>
        <td>economy_gdp_per_capita</td>
        <td>0.591683447360992</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>South Sudan</td>
        <td>Sub-Saharan Africa</td>
        <td>147</td>
        <td>3.59100008010864</td>
        <td>economy_gdp_per_capita</td>
        <td>0.39724862575531</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Liberia</td>
        <td>Sub-Saharan Africa</td>
        <td>148</td>
        <td>3.53299999237061</td>
        <td>economy_gdp_per_capita</td>
        <td>0.119041793048382</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Guinea</td>
        <td>Sub-Saharan Africa</td>
        <td>149</td>
        <td>3.50699996948242</td>
        <td>economy_gdp_per_capita</td>
        <td>0.244549930095673</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Togo</td>
        <td>Sub-Saharan Africa</td>
        <td>150</td>
        <td>3.49499988555908</td>
        <td>economy_gdp_per_capita</td>
        <td>0.305444717407227</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Rwanda</td>
        <td>Sub-Saharan Africa</td>
        <td>151</td>
        <td>3.47099995613098</td>
        <td>economy_gdp_per_capita</td>
        <td>0.368745893239975</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Syria</td>
        <td>Middle East and Northern Africa</td>
        <td>152</td>
        <td>3.46199989318848</td>
        <td>economy_gdp_per_capita</td>
        <td>0.777153134346008</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Tanzania</td>
        <td>Sub-Saharan Africa</td>
        <td>153</td>
        <td>3.34899997711182</td>
        <td>economy_gdp_per_capita</td>
        <td>0.511135876178741</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Burundi</td>
        <td>Sub-Saharan Africa</td>
        <td>154</td>
        <td>2.90499997138977</td>
        <td>economy_gdp_per_capita</td>
        <td>0.091622568666935</td>
    </tr>
    <tr>
        <td>2017</td>
        <td>Central African Republic</td>
        <td>Sub-Saharan Africa</td>
        <td>155</td>
        <td>2.69300007820129</td>
        <td>economy_gdp_per_capita</td>
        <td>0.0</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>Finland</td>
        <td>Western Europe</td>
        <td>1</td>
        <td>7.632</td>
        <td>economy_gdp_per_capita</td>
        <td>1.305</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>Norway</td>
        <td>Western Europe</td>
        <td>2</td>
        <td>7.594</td>
        <td>economy_gdp_per_capita</td>
        <td>1.456</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>Denmark</td>
        <td>Western Europe</td>
        <td>3</td>
        <td>7.555</td>
        <td>economy_gdp_per_capita</td>
        <td>1.351</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>Iceland</td>
        <td>Western Europe</td>
        <td>4</td>
        <td>7.495</td>
        <td>economy_gdp_per_capita</td>
        <td>1.343</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>Switzerland</td>
        <td>Western Europe</td>
        <td>5</td>
        <td>7.487</td>
        <td>economy_gdp_per_capita</td>
        <td>1.42</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>Netherlands</td>
        <td>Western Europe</td>
        <td>6</td>
        <td>7.441</td>
        <td>economy_gdp_per_capita</td>
        <td>1.361</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>Canada</td>
        <td>North America</td>
        <td>7</td>
        <td>7.328</td>
        <td>economy_gdp_per_capita</td>
        <td>1.33</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>New Zealand</td>
        <td>Australia and New Zealand</td>
        <td>8</td>
        <td>7.324</td>
        <td>economy_gdp_per_capita</td>
        <td>1.268</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>Sweden</td>
        <td>Western Europe</td>
        <td>9</td>
        <td>7.314</td>
        <td>economy_gdp_per_capita</td>
        <td>1.355</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>Australia</td>
        <td>Australia and New Zealand</td>
        <td>10</td>
        <td>7.272</td>
        <td>economy_gdp_per_capita</td>
        <td>1.34</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>United Kingdom</td>
        <td>Western Europe</td>
        <td>11</td>
        <td>7.19</td>
        <td>economy_gdp_per_capita</td>
        <td>1.244</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>Austria</td>
        <td>Western Europe</td>
        <td>12</td>
        <td>7.139</td>
        <td>economy_gdp_per_capita</td>
        <td>1.341</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>Costa Rica</td>
        <td>Latin America and Caribbean</td>
        <td>13</td>
        <td>7.072</td>
        <td>economy_gdp_per_capita</td>
        <td>1.01</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>Ireland</td>
        <td>Western Europe</td>
        <td>14</td>
        <td>6.977</td>
        <td>economy_gdp_per_capita</td>
        <td>1.448</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>Germany</td>
        <td>Western Europe</td>
        <td>15</td>
        <td>6.965</td>
        <td>economy_gdp_per_capita</td>
        <td>1.34</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>Belgium</td>
        <td>Western Europe</td>
        <td>16</td>
        <td>6.927</td>
        <td>economy_gdp_per_capita</td>
        <td>1.324</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>Luxembourg</td>
        <td>Western Europe</td>
        <td>17</td>
        <td>6.91</td>
        <td>economy_gdp_per_capita</td>
        <td>1.576</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>United States</td>
        <td>North America</td>
        <td>18</td>
        <td>6.886</td>
        <td>economy_gdp_per_capita</td>
        <td>1.398</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>Israel</td>
        <td>Middle East and Northern Africa</td>
        <td>19</td>
        <td>6.814</td>
        <td>economy_gdp_per_capita</td>
        <td>1.301</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>United Arab Emirates</td>
        <td>Middle East and Northern Africa</td>
        <td>20</td>
        <td>6.774</td>
        <td>economy_gdp_per_capita</td>
        <td>2.096</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>Czech Republic</td>
        <td>Central and Eastern Europe</td>
        <td>21</td>
        <td>6.711</td>
        <td>economy_gdp_per_capita</td>
        <td>1.233</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>Malta</td>
        <td>Western Europe</td>
        <td>22</td>
        <td>6.627</td>
        <td>economy_gdp_per_capita</td>
        <td>1.27</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>France</td>
        <td>Western Europe</td>
        <td>23</td>
        <td>6.489</td>
        <td>economy_gdp_per_capita</td>
        <td>1.293</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>Mexico</td>
        <td>Latin America and Caribbean</td>
        <td>24</td>
        <td>6.488</td>
        <td>economy_gdp_per_capita</td>
        <td>1.038</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>Chile</td>
        <td>Latin America and Caribbean</td>
        <td>25</td>
        <td>6.476</td>
        <td>economy_gdp_per_capita</td>
        <td>1.131</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>Taiwan</td>
        <td>Eastern Asia</td>
        <td>26</td>
        <td>6.441</td>
        <td>economy_gdp_per_capita</td>
        <td>1.365</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>Panama</td>
        <td>Latin America and Caribbean</td>
        <td>27</td>
        <td>6.43</td>
        <td>economy_gdp_per_capita</td>
        <td>1.112</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>Brazil</td>
        <td>Latin America and Caribbean</td>
        <td>28</td>
        <td>6.419</td>
        <td>economy_gdp_per_capita</td>
        <td>0.986</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>Argentina</td>
        <td>Latin America and Caribbean</td>
        <td>29</td>
        <td>6.388</td>
        <td>economy_gdp_per_capita</td>
        <td>1.073</td>
    </tr>
    <tr>
        <td>2018</td>
        <td>Guatemala</td>
        <td>Latin America and Caribbean</td>
        <td>30</td>
        <td>6.382</td>
        <td>economy_gdp_per_capita</td>
        <td>0.781</td>
    </tr>
</table>



Day 3 – You have been asked to create a report to identify which countries have ranked in the top 10
and in the bottom 10 in terms of happiness.
1. Generate a dataset that lists the top 10 and bottom 10 ranked happiest countries for each year.
2. Generate a dataset that lists the top 10 and bottom 10 ranked happiest countries of all time.


```sql
%%sql

--Generate a dataset that lists the top 10 and bottom 10 ranked happiest countries for each year.

with happiness_rank_year AS
	(
     Select 
     	year,
      	country,
      	happiness_rank,
      	row_number() over (partition by year order by happiness_rank) as rn_asc,
      	row_number() over (partition by year order by happiness_rank desc) as rn_desc
     from vw_world_happiness_index_consolidated
    )
Select 
	year,
    country,
    happiness_rank
from happiness_rank_year
where 
	rn_asc between 1 and 10
    OR
    rn_desc between 1 and 10;
    
--Generate a dataset that lists the top 10 and bottom 10 ranked happiest countries of all time.
--Assumption: ranking by aggregating happiness_score
With happiness_all_time_rank AS
	(
    SELECT
        country,
     	count(country) as frequency, --Potential give explanation why some are lower/higher
        round(sum(happiness_score),2) as total_happiness_score,
        row_number() over (order by round(sum(happiness_score),2)) as rn_asc,
        row_number() over (order by round(sum(happiness_score),2) desc) as rn_desc
    from vw_world_happiness_index_consolidated
    group by country
 	)
SELECT
	country,
    total_happiness_score,
    rn_desc as total_happiness_ranking,
    frequency
from happiness_all_time_rank
where 
	rn_asc between 1 and 10
    OR
    rn_desc between 1 and 10
order by total_happiness_score desc
```

     * sqlite:////Users/hochl/Downloads/apd_de_sql_screen.db
    Done.
    Done.





<table>
    <tr>
        <th>country</th>
        <th>total_happiness_score</th>
        <th>total_happiness_ranking</th>
        <th>frequency</th>
    </tr>
    <tr>
        <td>Denmark</td>
        <td>37.73</td>
        <td>1</td>
        <td>5</td>
    </tr>
    <tr>
        <td>Norway</td>
        <td>37.71</td>
        <td>2</td>
        <td>5</td>
    </tr>
    <tr>
        <td>Finland</td>
        <td>37.69</td>
        <td>3</td>
        <td>5</td>
    </tr>
    <tr>
        <td>Switzerland</td>
        <td>37.56</td>
        <td>4</td>
        <td>5</td>
    </tr>
    <tr>
        <td>Iceland</td>
        <td>37.56</td>
        <td>5</td>
        <td>5</td>
    </tr>
    <tr>
        <td>Netherlands</td>
        <td>37.02</td>
        <td>6</td>
        <td>5</td>
    </tr>
    <tr>
        <td>Canada</td>
        <td>36.75</td>
        <td>7</td>
        <td>5</td>
    </tr>
    <tr>
        <td>Sweden</td>
        <td>36.6</td>
        <td>8</td>
        <td>5</td>
    </tr>
    <tr>
        <td>New Zealand</td>
        <td>36.57</td>
        <td>9</td>
        <td>5</td>
    </tr>
    <tr>
        <td>Australia</td>
        <td>36.38</td>
        <td>10</td>
        <td>5</td>
    </tr>
    <tr>
        <td>Swaziland</td>
        <td>9.08</td>
        <td>161</td>
        <td>2</td>
    </tr>
    <tr>
        <td>Puerto Rico</td>
        <td>7.04</td>
        <td>162</td>
        <td>1</td>
    </tr>
    <tr>
        <td>Oman</td>
        <td>6.85</td>
        <td>163</td>
        <td>1</td>
    </tr>
    <tr>
        <td>Taiwan Province of China</td>
        <td>6.42</td>
        <td>164</td>
        <td>1</td>
    </tr>
    <tr>
        <td>Hong Kong S.A.R., China</td>
        <td>5.47</td>
        <td>165</td>
        <td>1</td>
    </tr>
    <tr>
        <td>North Macedonia</td>
        <td>5.27</td>
        <td>166</td>
        <td>1</td>
    </tr>
    <tr>
        <td>Somaliland region</td>
        <td>5.06</td>
        <td>167</td>
        <td>1</td>
    </tr>
    <tr>
        <td>Somaliland Region</td>
        <td>5.06</td>
        <td>168</td>
        <td>1</td>
    </tr>
    <tr>
        <td>Gambia</td>
        <td>4.52</td>
        <td>169</td>
        <td>1</td>
    </tr>
    <tr>
        <td>Djibouti</td>
        <td>4.37</td>
        <td>170</td>
        <td>1</td>
    </tr>
</table>


