# Amazon_Vine_Analysis
Perform ETL, determine bias, and analyze  Amazon Product Reviews

## Overview
The objective is to analyze Amazon reviews written by members of the paid Amazon Vine program. The Amazon Vine program is a service that allows manufacturers and publishers to receive reviews for their products. Companies like SellBy pay a small fee to Amazon and provide products to Amazon Vine members, who are then required to publish a review. 

The dataset chosen will be wireless products.

Overview of steps: Use PySpark to perform the ETL process to extract the dataset, transform the data, connect to an AWS RDS instance, and load the transformed data into pgAdmin. Then use Pandas to determine if there is any bias toward favorable reviews from Vine members in your dataset. Finally include a written summary of paid vs unpaid 5-Star ratings for bias.

## Resources

* Data Source: https://s3.amazonaws.com/amazon-reviews-pds/tsv/amazon_reviews_us_Wireless_v1_00.tsv.gz
* PgAdmin 4
* AWS RDS for PostgreSQL
* Google CoLab
 

 <img src="https://github.com/cjstreet/Amazon_Vine_Analysis/blob/main/Amazon_Vine_Analysis/Resources/pgAdmin.png" width="600" height="400">

## Results:

There is a bulleted list that addresses the three questions for unpaid and paid program reviews 

## ** How many Vine reviews (paid) and non-Vine (unpaid) reviews were there? **

```python
#The total number of paid reviews
total_paid = len(vine_paid_df)
total_paid
```
    613

### There are 613, total paid reviews.

```python
total_unpaid = len(vine_notPaid_df)
total_unpaid
```
    64968
    
### There are 64,968, total unpaid reviews.

## ** How many Vine reviews were 5 stars? How many non-Vine reviews were 5 stars?

```python
five_paid_df = vine_paid_df[vine_paid_df['star_rating'] == 5]
```

```python
total_five_star = len(five_paid_df)
total_five_star
```
    222

<span style="color:red">
There are 222, 5-star paid reviews. </style>


```python
total_five_star_unpaid = len(five_unpaid_df)
total_five_star_unpaid
```
    30543

### There are 30,543, 5-star reviews that are unpaid.

## ** What percentage of Vine reviews were 5 stars? What percentage of non-Vine reviews were 5 stars?

```python
# percentage of paid 5-star reviews

percent_5_star = total_five_star/total_paid
percent_5_star
```
    0.3621533442088091

### 36% of the paid reviews are 5-star ratings


```python
percent_5_star_unpaid = (total_five_star_unpaid /total_unpaid)
percent_5_star_unpaid
```
    0.47012375323236055

### 47% of the unpaid reviews are 5-star ratings


## Summary

State if there is any positivity bias for reviews in the Vine program. Use the results of your analysis to support your statement. Then, provide one additional analysis that you could do with the dataset to support your statement.

## Vine Review Analysis by CStreet

Import Dependencies

```python
import pandas as pd
import os
```
```python
vine_data = os.path.join('vine_table.csv')
vine_df = pd.read_csv(vine_data)
```

```python
vine_df.head()
```

<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>review_id</th>
      <th>star_rating</th>
      <th>helpful_votes</th>
      <th>total_votes</th>
      <th>vine</th>
      <th>verified_purchase</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>R3W4P9UBGNGH1U</td>
      <td>2.0</td>
      <td>1.0</td>
      <td>3.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
    <tr>
      <th>1</th>
      <td>R15V54KBMTQWAY</td>
      <td>4.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>N</td>
      <td>N</td>
    </tr>
    <tr>
      <th>2</th>
      <td>RY8I449HNXSVF</td>
      <td>5.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
    <tr>
      <th>3</th>
      <td>R18TLJYCKJFLSR</td>
      <td>5.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
    <tr>
      <th>4</th>
      <td>R1NK26SWS53B8Q</td>
      <td>5.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
  </tbody>
</table>
</div>

### Filter the data and create a new DataFrame or table to retrieve all the rows where the total_votes count is equal to or greater than 20 to pick reviews that are more likely to be helpful and to avoid having division by zero errors later on.

```python
#filtered dataframe
vine_filtered_df = vine_df.query('total_votes >= 20')
```

```python
vine_filtered_df.head()
```

<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>review_id</th>
      <th>star_rating</th>
      <th>helpful_votes</th>
      <th>total_votes</th>
      <th>vine</th>
      <th>verified_purchase</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>116</th>
      <td>R2WOW0TURNXB26</td>
      <td>3.0</td>
      <td>54.0</td>
      <td>59.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
    <tr>
      <th>197</th>
      <td>R13VL62Y2HBQ0B</td>
      <td>5.0</td>
      <td>15.0</td>
      <td>21.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
    <tr>
      <th>307</th>
      <td>R22G55KAPZKJQV</td>
      <td>4.0</td>
      <td>20.0</td>
      <td>21.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
    <tr>
      <th>1012</th>
      <td>R1610PGTJS7G3N</td>
      <td>2.0</td>
      <td>28.0</td>
      <td>44.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
    <tr>
      <th>2265</th>
      <td>RLQL04BL0QXOJ</td>
      <td>4.0</td>
      <td>45.0</td>
      <td>47.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
  </tbody>
</table>
</div>


### Filter the new DataFrame or table created in Step 1 and create a new DataFrame or table to retrieve all the rows where the number of helpful_votes divided by total_votes is equal to or greater than 50%.


```python
vine_filtered_df = vine_filtered_df.query('helpful_votes/total_votes >= .50')
```

```python
vine_filtered_df.head()
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>review_id</th>
      <th>star_rating</th>
      <th>helpful_votes</th>
      <th>total_votes</th>
      <th>vine</th>
      <th>verified_purchase</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>116</th>
      <td>R2WOW0TURNXB26</td>
      <td>3.0</td>
      <td>54.0</td>
      <td>59.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
    <tr>
      <th>197</th>
      <td>R13VL62Y2HBQ0B</td>
      <td>5.0</td>
      <td>15.0</td>
      <td>21.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
    <tr>
      <th>307</th>
      <td>R22G55KAPZKJQV</td>
      <td>4.0</td>
      <td>20.0</td>
      <td>21.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
    <tr>
      <th>1012</th>
      <td>R1610PGTJS7G3N</td>
      <td>2.0</td>
      <td>28.0</td>
      <td>44.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
    <tr>
      <th>2265</th>
      <td>RLQL04BL0QXOJ</td>
      <td>4.0</td>
      <td>45.0</td>
      <td>47.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
  </tbody>
</table>
</div>

### Filter the DataFrame or table created in Step 2, and create a new DataFrame or table that retrieves all the rows where a review was written as part of the Vine program (paid), vine == 'Y'.

```python
vine_filtered_df.dtypes
```
    review_id             object
    star_rating          float64
    helpful_votes        float64
    total_votes          float64
    vine                  object
    verified_purchase     object
    dtype: object

```python
vine_paid_df = vine_filtered_df[vine_filtered_df["vine"] == "Y"]
```

```python
vine_paid_df.tail(15)
```
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>review_id</th>
      <th>star_rating</th>
      <th>helpful_votes</th>
      <th>total_votes</th>
      <th>vine</th>
      <th>verified_purchase</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>8819452</th>
      <td>R1GPYXSB76U4QF</td>
      <td>3.0</td>
      <td>251.0</td>
      <td>266.0</td>
      <td>Y</td>
      <td>N</td>
    </tr>
    <tr>
      <th>8819520</th>
      <td>R1S5PEW9FYV4Y7</td>
      <td>5.0</td>
      <td>56.0</td>
      <td>57.0</td>
      <td>Y</td>
      <td>N</td>
    </tr>
    <tr>
      <th>8819534</th>
      <td>RLIMMC9RD6G6O</td>
      <td>5.0</td>
      <td>415.0</td>
      <td>423.0</td>
      <td>Y</td>
      <td>N</td>
    </tr>
    <tr>
      <th>8819775</th>
      <td>RVKT49SXK12LV</td>
      <td>5.0</td>
      <td>377.0</td>
      <td>387.0</td>
      <td>Y</td>
      <td>N</td>
    </tr>
    <tr>
      <th>8847570</th>
      <td>R1T2GB0YIQ90YH</td>
      <td>3.0</td>
      <td>85.0</td>
      <td>94.0</td>
      <td>Y</td>
      <td>N</td>
    </tr>
    <tr>
      <th>8880631</th>
      <td>R28IX4P4PWVELW</td>
      <td>5.0</td>
      <td>42.0</td>
      <td>47.0</td>
      <td>Y</td>
      <td>N</td>
    </tr>
    <tr>
      <th>8883288</th>
      <td>RHCQE4GXO2USK</td>
      <td>3.0</td>
      <td>13.0</td>
      <td>23.0</td>
      <td>Y</td>
      <td>N</td>
    </tr>
    <tr>
      <th>8885056</th>
      <td>R2U3KAROWWVDDG</td>
      <td>4.0</td>
      <td>26.0</td>
      <td>26.0</td>
      <td>Y</td>
      <td>N</td>
    </tr>
    <tr>
      <th>8885687</th>
      <td>R1M4QZ4UUNG55D</td>
      <td>5.0</td>
      <td>141.0</td>
      <td>155.0</td>
      <td>Y</td>
      <td>Y</td>
    </tr>
    <tr>
      <th>8887121</th>
      <td>R2HFFG2O78SOC4</td>
      <td>4.0</td>
      <td>269.0</td>
      <td>274.0</td>
      <td>Y</td>
      <td>N</td>
    </tr>
    <tr>
      <th>8887352</th>
      <td>R1S2HIHNB0FJC0</td>
      <td>2.0</td>
      <td>180.0</td>
      <td>196.0</td>
      <td>Y</td>
      <td>N</td>
    </tr>
    <tr>
      <th>8887434</th>
      <td>R3ADVLGTBP0TNX</td>
      <td>4.0</td>
      <td>36.0</td>
      <td>43.0</td>
      <td>Y</td>
      <td>N</td>
    </tr>
    <tr>
      <th>8910450</th>
      <td>R9OB51UW8R4EW</td>
      <td>3.0</td>
      <td>13.0</td>
      <td>23.0</td>
      <td>Y</td>
      <td>N</td>
    </tr>
    <tr>
      <th>8912079</th>
      <td>R3OT72XT5WKCYN</td>
      <td>5.0</td>
      <td>27.0</td>
      <td>40.0</td>
      <td>Y</td>
      <td>N</td>
    </tr>
    <tr>
      <th>8912399</th>
      <td>R2Y9JUXDM65FZW</td>
      <td>2.0</td>
      <td>17.0</td>
      <td>30.0</td>
      <td>Y</td>
      <td>N</td>
    </tr>
  </tbody>
</table>
</div>

### Retrieve all the rows where the review was not part of the Vine program (unpaid)

```python
vine_notPaid_df = vine_filtered_df[vine_filtered_df["vine"] == "N"]
```

```python
vine_notPaid_df.head(15)
```
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>review_id</th>
      <th>star_rating</th>
      <th>helpful_votes</th>
      <th>total_votes</th>
      <th>vine</th>
      <th>verified_purchase</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>116</th>
      <td>R2WOW0TURNXB26</td>
      <td>3.0</td>
      <td>54.0</td>
      <td>59.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
    <tr>
      <th>197</th>
      <td>R13VL62Y2HBQ0B</td>
      <td>5.0</td>
      <td>15.0</td>
      <td>21.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
    <tr>
      <th>307</th>
      <td>R22G55KAPZKJQV</td>
      <td>4.0</td>
      <td>20.0</td>
      <td>21.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
    <tr>
      <th>1012</th>
      <td>R1610PGTJS7G3N</td>
      <td>2.0</td>
      <td>28.0</td>
      <td>44.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
    <tr>
      <th>2265</th>
      <td>RLQL04BL0QXOJ</td>
      <td>4.0</td>
      <td>45.0</td>
      <td>47.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
    <tr>
      <th>2731</th>
      <td>R2AYJHH8WJNGAU</td>
      <td>4.0</td>
      <td>98.0</td>
      <td>104.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
    <tr>
      <th>3007</th>
      <td>R111DJA10Y6CMU</td>
      <td>5.0</td>
      <td>26.0</td>
      <td>39.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
    <tr>
      <th>3164</th>
      <td>R2EE2TR4MRDV0U</td>
      <td>5.0</td>
      <td>53.0</td>
      <td>59.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
    <tr>
      <th>3368</th>
      <td>RD4A80I5JDHED</td>
      <td>5.0</td>
      <td>69.0</td>
      <td>77.0</td>
      <td>N</td>
      <td>N</td>
    </tr>
    <tr>
      <th>3376</th>
      <td>R1GU6IYZQWQE8X</td>
      <td>2.0</td>
      <td>24.0</td>
      <td>25.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
    <tr>
      <th>3706</th>
      <td>RZOPM62JMW97V</td>
      <td>2.0</td>
      <td>103.0</td>
      <td>108.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
    <tr>
      <th>5049</th>
      <td>R1D3NR5GREEXXJ</td>
      <td>5.0</td>
      <td>57.0</td>
      <td>58.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
    <tr>
      <th>5162</th>
      <td>R24BMEHX5EWGEY</td>
      <td>2.0</td>
      <td>26.0</td>
      <td>30.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
    <tr>
      <th>5185</th>
      <td>R2WYKBQS8OR08O</td>
      <td>5.0</td>
      <td>168.0</td>
      <td>176.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
    <tr>
      <th>5777</th>
      <td>R3DL0Y1KWYLD5X</td>
      <td>5.0</td>
      <td>36.0</td>
      <td>37.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
  </tbody>
</table>
</div>

### The total number of reviews, the number of 5-star reviews, and the percentage of 5-star reviews for the two types of review (paid vs unpaid)

## PAID


```python
#The total number of paid reviews
total_paid = len(vine_paid_df)
total_paid
```

    613

### There are 613, total paid reviews.

```python
five_paid_df = vine_paid_df[vine_paid_df['star_rating'] == 5]
five_paid_df.head()
```

<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>review_id</th>
      <th>star_rating</th>
      <th>helpful_votes</th>
      <th>total_votes</th>
      <th>vine</th>
      <th>verified_purchase</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>23444</th>
      <td>R6V9SHMMG5M8F</td>
      <td>5.0</td>
      <td>101.0</td>
      <td>110.0</td>
      <td>Y</td>
      <td>N</td>
    </tr>
    <tr>
      <th>193753</th>
      <td>R3SRW1E8J56IGV</td>
      <td>5.0</td>
      <td>262.0</td>
      <td>281.0</td>
      <td>Y</td>
      <td>N</td>
    </tr>
    <tr>
      <th>229126</th>
      <td>R2WBPX441TH495</td>
      <td>5.0</td>
      <td>202.0</td>
      <td>221.0</td>
      <td>Y</td>
      <td>N</td>
    </tr>
    <tr>
      <th>238470</th>
      <td>R2BYBSYHS66ZN8</td>
      <td>5.0</td>
      <td>109.0</td>
      <td>114.0</td>
      <td>Y</td>
      <td>N</td>
    </tr>
    <tr>
      <th>261805</th>
      <td>R2IXC6U7W4OCQ9</td>
      <td>5.0</td>
      <td>185.0</td>
      <td>201.0</td>
      <td>Y</td>
      <td>N</td>
    </tr>
  </tbody>
</table>
</div>

```python
total_five_star = len(five_paid_df)
total_five_star
```

    222

### There are 222, 5-star paid reviews.


```python
# percentage of paid 5-star reviews

percent_5_star = total_five_star/total_paid
percent_5_star
```
    0.3621533442088091

### 36% of the paid reviews are 5-star ratings

## Unpaid


```python
total_unpaid = len(vine_notPaid_df)
total_unpaid
```




    64968



### There are 64,968, total unpaid reviews.


```python
five_unpaid_df = vine_notPaid_df[vine_notPaid_df['star_rating'] == 5]
five_unpaid_df.head()
```


<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>review_id</th>
      <th>star_rating</th>
      <th>helpful_votes</th>
      <th>total_votes</th>
      <th>vine</th>
      <th>verified_purchase</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>197</th>
      <td>R13VL62Y2HBQ0B</td>
      <td>5.0</td>
      <td>15.0</td>
      <td>21.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
    <tr>
      <th>3007</th>
      <td>R111DJA10Y6CMU</td>
      <td>5.0</td>
      <td>26.0</td>
      <td>39.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
    <tr>
      <th>3164</th>
      <td>R2EE2TR4MRDV0U</td>
      <td>5.0</td>
      <td>53.0</td>
      <td>59.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
    <tr>
      <th>3368</th>
      <td>RD4A80I5JDHED</td>
      <td>5.0</td>
      <td>69.0</td>
      <td>77.0</td>
      <td>N</td>
      <td>N</td>
    </tr>
    <tr>
      <th>5049</th>
      <td>R1D3NR5GREEXXJ</td>
      <td>5.0</td>
      <td>57.0</td>
      <td>58.0</td>
      <td>N</td>
      <td>Y</td>
    </tr>
  </tbody>
</table>
</div>




```python
total_five_star_unpaid = len(five_unpaid_df)
total_five_star_unpaid
```




    30543



### There are 30,543, 5-star reviews that are unpaid.


```python
percent_5_star_unpaid = (total_five_star_unpaid /total_unpaid)
percent_5_star_unpaid
```




    0.47012375323236055



### 47% of the unpaid reviews are 5-star ratings


```python

```


