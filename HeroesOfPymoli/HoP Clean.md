

```python
import pandas as pd
import matplotlib.pyplot as plt
```


```python
# Read data
data = 'purchase_data.json'
df = pd.read_json(data)
```

# Player Count


```python
# Grouping by SN and counting values
grouped_players = df.groupby('SN')
df1 = grouped_players.count()
total_p = df1['Age'].count()

# Creating DataFrame
total_players = pd.DataFrame({
    'Total Players': [total_p]
})
total_players
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
      <th>Total Players</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>573</td>
    </tr>
  </tbody>
</table>
</div>



# Purchasing Analysis (Total)


```python
# Grouping items and counting unique items
grouped_items = df.groupby('Item ID')
df1 = grouped_items.count()
unique_items = df1['Age'].count()

# Calculating average price per item
avg_price = round(df['Price'].mean(),2)
avg_price = '${:.2f}'.format(avg_price)

# Calculating number of total purchases
total_purchases = df['Item ID'].count()

# Calculating total revenue
total_revenue = df['Price'].sum()
total_revenue = '${:,.2f}'.format(total_revenue)

# Creating DataFrame w/ above variables
purchasing_analysis_total = pd.DataFrame({
    'Number of Unique Items': [unique_items],
    'Average Price': [avg_price],
    'Number of Purchases': [total_purchases],
    'Total Revenue': [total_revenue]
})

purchasing_analysis_total = purchasing_analysis_total[
    [
        'Number of Unique Items', 
        'Average Price',
        'Number of Purchases',
        'Total Revenue'
    ]
]
purchasing_analysis_total
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
      <th>Number of Unique Items</th>
      <th>Average Price</th>
      <th>Number of Purchases</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>183</td>
      <td>$2.93</td>
      <td>780</td>
      <td>$2,286.33</td>
    </tr>
  </tbody>
</table>
</div>



#  Gender Demographics


```python
# Getting unique users
users = df.drop_duplicates('SN')

# Counting unique users
gender_counts = users['Gender'].value_counts()

# Counting unique users by gender
male = gender_counts['Male']
female = gender_counts['Female']
other = gender_counts['Other / Non-Disclosed']

# Cacluating gender percentages
percent_male = round((male/total_p)*100,2)
percent_female = round((female/total_p)*100,2)
percent_other = round((other/total_p)*100,2)

# Creating DataFrame
gender_demographics = pd.DataFrame({
    'Gender': ['Male', 'Female', 'Other / Non-Disclosed'],
    'Percentage of Players': [percent_male, percent_female, percent_other],
    'Total Count': [male, female, other]
})

gender_demographics.set_index('Gender')
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
      <th>Percentage of Players</th>
      <th>Total Count</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>81.15</td>
      <td>465</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>17.45</td>
      <td>100</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>1.40</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>



# Purchasing Analysis (Gender)


```python
# Calculating number of users w/in each gender
gender_counts = df['Gender'].value_counts()
gender_counts_df = gender_counts.to_frame()
gender_counts_df = gender_counts_df.rename(columns={'Gender':'Purchase Count'})

# Calculating average price by gender
gender = df.groupby('Gender')
gender_price = gender['Price'].mean()

gender_price_df = gender_price.to_frame()
gender_price_df['Price'] = gender_price_df['Price'].map('${:.2f}'.format)
gender_price_df = gender_price_df.rename(columns={'Price':'Average Purchase Price'})

# Calculating total price by gender
gender_value = gender['Price'].sum()

gender_value_df = gender_value.to_frame()
gender_value_df['Price'] = gender_value_df['Price'].map('${:,.2f}'.format)
gender_value_df = gender_value_df.rename(columns={'Price':'Total Purchase Value'})

# Calculating normalized totals (male)
df_reset_index = df.set_index('Gender')

df_male = df_reset_index.loc['Male']

df_male_users = df_male.groupby('SN')

df_male_value = df_male_users.sum()

male_normalized_total = df_male_value['Price'].mean()
male_normalized_total = '${:,.2f}'.format(male_normalized_total)

# Calculating normalized totals (female)
df_female = df_reset_index.loc['Female']

df_female_users = df_female.groupby('SN')

df_female_value = df_female_users.sum()

female_normalized_total = df_female_value['Price'].mean()
female_normalized_total = '${:,.2f}'.format(female_normalized_total)

# Calculating normalized totals (other)
df_other = df_reset_index.loc['Other / Non-Disclosed']

df_other_users = df_other.groupby('SN')

df_other_value = df_other_users.sum()

other_normalized_total = df_other_value['Price'].mean()
other_normalized_total = '${:,.2f}'.format(other_normalized_total)

# Creating normalized totals DataFrame
normalized_totals = pd.DataFrame({
    'Gender': ['Male', 'Female', 'Other / Non-Disclosed'],
    'Normalized Totals': [male_normalized_total, female_normalized_total, other_normalized_total]
})

normalized_totals_df = normalized_totals.set_index('Gender')

# mergeing DataFrames
merge1 = gender_price_df.merge(gender_value_df, left_index=True, right_index=True)
merge2 = merge1.merge(gender_counts_df, left_index=True, right_index=True)
merge3 = merge2.merge(normalized_totals_df, left_index=True, right_index=True)

# Reordering columns in DataFrame
purchasing_analysis_gender = merge3[
    [
        'Purchase Count', 
        'Average Purchase Price',
        'Total Purchase Value',
        'Normalized Totals'
    ]
]
purchasing_analysis_gender
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
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>136</td>
      <td>$2.82</td>
      <td>$382.91</td>
      <td>$3.83</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>633</td>
      <td>$2.95</td>
      <td>$1,867.68</td>
      <td>$4.02</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>11</td>
      <td>$3.25</td>
      <td>$35.74</td>
      <td>$4.47</td>
    </tr>
  </tbody>
</table>
</div>



# Age Demographics


```python
# Creating bins
bins = [0, 9, 14, 19, 24, 29, 34, 39, ((df['Age'].max())+1)]

# Creating bin labels
labels = ['<10', '10-14', '15-19','20-24', '25-29', '30-34', '35-39', '40+']

# Grouping by SN and grabbing max user age
SN = df.groupby('SN')
user_age = SN.max()

# Creating new column age bins from Age column
user_age['bin'] = pd.cut(user_age['Age'], bins, labels=labels )

# Counting values per age bin
bin_df = user_age['bin'].value_counts()

# Creating DataFrame
age_count_df = bin_df.to_frame()
age_demographics = age_count_df.rename(columns={'bin':'Total Count'})
age_demographics['Percentage of Players'] = round((age_demographics['Total Count']/ total_p)*100,2)
age_demographics = age_demographics[['Percentage of Players', 'Total Count']]
age_demographics = age_demographics.reindex(labels)

age_demographics
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
      <th>Percentage of Players</th>
      <th>Total Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>3.32</td>
      <td>19</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>4.01</td>
      <td>23</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>17.45</td>
      <td>100</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>45.20</td>
      <td>259</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>15.18</td>
      <td>87</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>8.20</td>
      <td>47</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>4.71</td>
      <td>27</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>1.92</td>
      <td>11</td>
    </tr>
  </tbody>
</table>
</div>



#  Purchasing Analysis (Age)


```python
# Creating bins
bins = [0, 9, 14, 19, 24, 29, 34, 39, 200]

# Creating bin labels
labels = ['<10', '10-14', '15-19','20-24', '25-29', '30-34', '35-39', '40+']

# Creating new column age bins to bin ages
df["Age Bins"] = pd.cut(df["Age"], bins, labels=labels)

# Calculating count per age bin
grouped_bins = df.groupby('Age Bins')
purchasing_analysis = grouped_bins['Item ID'].count()
purchasing_analysis = purchasing_analysis.to_frame()
purchasing_analysis = purchasing_analysis.rename(columns={'Item ID':'Purchase Count'})

# Calculating average price per age bin
grouped_bins = df.groupby('Age Bins')
average_pur_price = grouped_bins['Price'].mean()
average_pur_price = average_pur_price.to_frame()
average_pur_price['Price'] = average_pur_price['Price'].map('${:,.2f}'.format)

average_pur_price = average_pur_price.rename(columns={'Price':'Average Purchase Price'})

# Calculating total price per age bin
grouped_bins = df.groupby('Age Bins')
total_pur_price = grouped_bins['Price'].sum()
total_pur_price = total_pur_price.to_frame()
total_pur_price['Price'] = total_pur_price['Price'].map('${:,.2f}'.format)
total_pur_price = total_pur_price.rename(columns={'Price':'Total Purchase Price'})

# Calculating normalized total (<10)
df_reset_index = df.set_index('Age Bins')

df_lt10 = df_reset_index.loc['<10']

df_lt10_users = df_lt10.groupby('SN')

df_lt10_value = df_lt10_users.sum()

df_lt10_normalized_total = df_lt10_value['Price'].mean()
df_lt10_normalized_total = '${:,.2f}'.format(df_lt10_normalized_total)

# Calculating normalized total (10-14)
df_reset_index = df.set_index('Age Bins')

df_1014 = df_reset_index.loc['10-14']

df_1014_users = df_1014.groupby('SN')

df_1014_value = df_1014_users.sum()

df_1014_normalized_total = df_1014_value['Price'].mean()
df_1014_normalized_total = '${:,.2f}'.format(df_1014_normalized_total)

# Calculating normalized total (15-19)
df_reset_index = df.set_index('Age Bins')

df_1519 = df_reset_index.loc['15-19']

df_1519_users = df_1519.groupby('SN')

df_1519_value = df_1519_users.sum()

df_1519_normalized_total = df_1519_value['Price'].mean()
df_1519_normalized_total = '${:,.2f}'.format(df_1519_normalized_total)

# Calculating normalized total (20-24)
df_reset_index = df.set_index('Age Bins')

df_2024 = df_reset_index.loc['20-24']

df_2024_users = df_2024.groupby('SN')

df_2024_value = df_2024_users.sum()

df_2024_normalized_total = df_2024_value['Price'].mean()
df_2024_normalized_total = '${:,.2f}'.format(df_2024_normalized_total)

# Calculating normalized total (25-29)
df_reset_index = df.set_index('Age Bins')

df_2529 = df_reset_index.loc['25-29']

df_2529_users = df_2529.groupby('SN')

df_2529_value = df_2529_users.sum()

df_2529_normalized_total = df_2529_value['Price'].mean()
df_2529_normalized_total = '${:,.2f}'.format(df_2529_normalized_total)

# Calculating normalized total (30-34)
df_reset_index = df.set_index('Age Bins')

df_3034 = df_reset_index.loc['30-34']

df_3034_users = df_3034.groupby('SN')

df_3034_value = df_3034_users.sum()

df_3034_normalized_total = df_3034_value['Price'].mean()
df_3034_normalized_total = '${:,.2f}'.format(df_3034_normalized_total)

# Calculating normalized total (35-39)
df_reset_index = df.set_index('Age Bins')

df_3539 = df_reset_index.loc['35-39']

df_3539_users = df_3539.groupby('SN')

df_3539_value = df_3539_users.sum()

df_3539_normalized_total = df_3539_value['Price'].mean()
df_3539_normalized_total = '${:,.2f}'.format(df_3539_normalized_total)

# Calculating normalized total (40+)
df_reset_index = df.set_index('Age Bins')

df_gt40 = df_reset_index.loc['40+']

df_gt40_users = df_gt40.groupby('SN')

df_gt40_value = df_gt40_users.sum()

df_gt40_normalized_total = df_gt40_value['Price'].mean()
df_gt40_normalized_total = '${:,.2f}'.format(df_gt40_normalized_total)

# Creating normalized totals DataFrame
pa_normalized_totals = pd.DataFrame({
    'Age Bins': labels,
    'Normalized Totals': [df_lt10_normalized_total, df_1014_normalized_total, 
                          df_1519_normalized_total, df_2024_normalized_total,
                          df_2529_normalized_total, df_3034_normalized_total,
                          df_3539_normalized_total, df_gt40_normalized_total]
})

pa_normalized_totals = pa_normalized_totals.set_index('Age Bins')

# Merging DataFrames
merge1 = purchasing_analysis.merge(average_pur_price, left_index=True, right_index=True)
merge2 = merge1.merge(total_pur_price, left_index=True, right_index=True)
merge3 = merge2.merge(pa_normalized_totals, left_index=True, right_index=True)

purchasing_analysis_age = merge3.reindex(['10-14', '15-19','20-24', '25-29', '30-34', '35-39', '40+', '<10'])
purchasing_analysis_age
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
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Price</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Age Bins</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>10-14</th>
      <td>35</td>
      <td>$2.77</td>
      <td>$96.95</td>
      <td>$4.22</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>133</td>
      <td>$2.91</td>
      <td>$386.42</td>
      <td>$3.86</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>336</td>
      <td>$2.91</td>
      <td>$978.77</td>
      <td>$3.78</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>125</td>
      <td>$2.96</td>
      <td>$370.33</td>
      <td>$4.26</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>64</td>
      <td>$3.08</td>
      <td>$197.25</td>
      <td>$4.20</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>42</td>
      <td>$2.84</td>
      <td>$119.40</td>
      <td>$4.42</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>17</td>
      <td>$3.16</td>
      <td>$53.75</td>
      <td>$4.89</td>
    </tr>
    <tr>
      <th>&lt;10</th>
      <td>28</td>
      <td>$2.98</td>
      <td>$83.46</td>
      <td>$4.39</td>
    </tr>
  </tbody>
</table>
</div>



# Top Spenders


```python
# Calculating number of users
user_sn_counts = df['SN'].value_counts()
user_sn_counts = user_sn_counts.to_frame()
user_sn_counts = user_sn_counts.rename(columns={'SN':'Purchase Count'})

# Grouping by SN and calculating average price per SN
user_sn = df.groupby('SN')
avg_pur_pr = user_sn.mean()
avg_pur_pr = avg_pur_pr.rename(columns={'Price':'Average Purchase Price'})
avg_pur_pr = avg_pur_pr['Average Purchase Price'].to_frame() 
avg_pur_pr['Average Purchase Price'] = avg_pur_pr['Average Purchase Price'].map('${:,.2f}'.format)

# Calculating total price per SN
user_sn = df.groupby('SN')
tot_pur_pr = user_sn.sum()
tot_pur_pr = tot_pur_pr.rename(columns={'Price':'Total Purchase Value'})
tot_pur_pr = tot_pur_pr['Total Purchase Value'].to_frame() 

# Merging DataFrames
merge1 = user_sn_counts.merge(avg_pur_pr, left_index=True, right_index=True)
merge1
merge2 = merge1.merge(tot_pur_pr, left_index=True, right_index=True)

top_spenders = merge2.sort_values(by='Total Purchase Value', ascending=False)
top_spenders['Total Purchase Value'] = top_spenders['Total Purchase Value'].map('${:,.2f}'.format)

top_spenders.head()
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
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Undirrala66</th>
      <td>5</td>
      <td>$3.41</td>
      <td>$17.06</td>
    </tr>
    <tr>
      <th>Saedue76</th>
      <td>4</td>
      <td>$3.39</td>
      <td>$13.56</td>
    </tr>
    <tr>
      <th>Mindimnya67</th>
      <td>4</td>
      <td>$3.18</td>
      <td>$12.74</td>
    </tr>
    <tr>
      <th>Haellysu29</th>
      <td>3</td>
      <td>$4.24</td>
      <td>$12.73</td>
    </tr>
    <tr>
      <th>Eoda93</th>
      <td>3</td>
      <td>$3.86</td>
      <td>$11.58</td>
    </tr>
  </tbody>
</table>
</div>



#  Most Popular Items


```python
# Calculating number of items
item_id_counts = df['Item ID'].value_counts()
item_id_counts = item_id_counts.to_frame()
item_id_counts = item_id_counts.rename(columns={'Item ID':'Purchase Count'})

# Removing duplicate Item and grabbing Item Name for each Item ID
items = df.drop_duplicates('Item ID')

item_index = items.set_index('Item ID')
item_index['Item Name'].to_frame()

item_id_to_name = item_index['Item Name'].to_frame()

# Grabbing Item Price for each Item ID
item_index['Price'].to_frame()

item_id_to_price = item_index['Price'].to_frame()
item_id_to_price = item_id_to_price.rename(columns={'Price': 'Item Price'})

# Calculating total amount spent on each item
item_grouped = df.groupby('Item ID')

item_total_value = item_grouped['Price'].sum()

item_total_value_df = item_total_value.to_frame()

item_total_value_df = item_total_value_df.rename(columns={'Price':'Total Purchase Value'})

# Merging DataFrames
merge1 = item_id_counts.merge(item_id_to_name, left_index=True, right_index=True)
merge2 = merge1.merge(item_id_to_price, left_index=True, right_index=True)
merge3 = merge2.merge(item_total_value_df, left_index=True, right_index=True)

most_popular_items = merge3.sort_values(by=['Purchase Count'], ascending=False)
most_popular_items = most_popular_items[['Item Name', 'Purchase Count', 'Item Price', 'Total Purchase Value']]
most_popular_items = most_popular_items.drop([34]).head()
most_popular_items['Total Purchase Value'] = most_popular_items['Total Purchase Value'].map('${:,.2f}'.format)
most_popular_items['Item Price'] = most_popular_items['Item Price'].map('${:,.2f}'.format)


most_popular_items
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
      <th>Item Name</th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>84</th>
      <td>Arcane Gem</td>
      <td>11</td>
      <td>$2.23</td>
      <td>$24.53</td>
    </tr>
    <tr>
      <th>39</th>
      <td>Betrayal, Whisper of Grieving Widows</td>
      <td>11</td>
      <td>$2.35</td>
      <td>$25.85</td>
    </tr>
    <tr>
      <th>31</th>
      <td>Trickster</td>
      <td>9</td>
      <td>$2.07</td>
      <td>$18.63</td>
    </tr>
    <tr>
      <th>175</th>
      <td>Woeful Adamantite Claymore</td>
      <td>9</td>
      <td>$1.24</td>
      <td>$11.16</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Serenity</td>
      <td>9</td>
      <td>$1.49</td>
      <td>$13.41</td>
    </tr>
  </tbody>
</table>
</div>



#  Most Profitable Items


```python
# Adjusting preivious variable to perform math on Total Purchase Value rather than on Purchase Count
most_profitable_items = merge3.sort_values(by=['Total Purchase Value'], ascending=False)
most_profitable_items['Total Purchase Value'] = most_profitable_items['Total Purchase Value'].map('${:,.2f}'.format)
most_profitable_items['Item Price'] = most_profitable_items['Item Price'].map('${:,.2f}'.format)
most_profitable_items = most_profitable_items[['Item Name', 'Purchase Count', 'Item Price', 'Total Purchase Value']]

most_profitable_items.head()
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
      <th>Item Name</th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>34</th>
      <td>Retribution Axe</td>
      <td>9</td>
      <td>$4.14</td>
      <td>$37.26</td>
    </tr>
    <tr>
      <th>115</th>
      <td>Spectral Diamond Doomblade</td>
      <td>7</td>
      <td>$4.25</td>
      <td>$29.75</td>
    </tr>
    <tr>
      <th>32</th>
      <td>Orenmir</td>
      <td>6</td>
      <td>$4.95</td>
      <td>$29.70</td>
    </tr>
    <tr>
      <th>103</th>
      <td>Singed Scalpel</td>
      <td>6</td>
      <td>$4.87</td>
      <td>$29.22</td>
    </tr>
    <tr>
      <th>107</th>
      <td>Splitter, Foe Of Subtlety</td>
      <td>8</td>
      <td>$3.61</td>
      <td>$28.88</td>
    </tr>
  </tbody>
</table>
</div>


