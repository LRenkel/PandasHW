

```python
import pandas as pd
import numpy as np
```


```python
df = pd.read_json('purchase_data.json')
df.head()
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
      <th>Age</th>
      <th>Gender</th>
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Price</th>
      <th>SN</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>38</td>
      <td>Male</td>
      <td>165</td>
      <td>Bone Crushing Silver Skewer</td>
      <td>3.37</td>
      <td>Aelalis34</td>
    </tr>
    <tr>
      <th>1</th>
      <td>21</td>
      <td>Male</td>
      <td>119</td>
      <td>Stormbringer, Dark Blade of Ending Misery</td>
      <td>2.32</td>
      <td>Eolo46</td>
    </tr>
    <tr>
      <th>2</th>
      <td>34</td>
      <td>Male</td>
      <td>174</td>
      <td>Primitive Blade</td>
      <td>2.46</td>
      <td>Assastnya25</td>
    </tr>
    <tr>
      <th>3</th>
      <td>21</td>
      <td>Male</td>
      <td>92</td>
      <td>Final Critic</td>
      <td>1.36</td>
      <td>Pheusrical25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>23</td>
      <td>Male</td>
      <td>63</td>
      <td>Stormfury Mace</td>
      <td>1.27</td>
      <td>Aela59</td>
    </tr>
  </tbody>
</table>
</div>



## Player Count


```python
player_count = df['SN'].nunique()
player_count_df = pd.DataFrame({
    'Total Players': [player_count]
})
player_count_df
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



## Purchasing Analysis Total


```python
item_count = df['Item ID'].nunique()
average_price = '${}'.format(round(df['Price'].mean(),2))
purchase_count = len(df)
total_rev = '${}'.format(round(df['Price'].sum(),2))

```


```python
purchasing_analysis_df = pd.DataFrame({
    "Number of Unique Items": [item_count],
    "Average Price": [average_price],
    "Number of Purchases": [purchase_count],
    "Total Revenue": [total_rev]
})
purchasing_analysis_df
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
      <th>Average Price</th>
      <th>Number of Purchases</th>
      <th>Number of Unique Items</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>$2.93</td>
      <td>780</td>
      <td>183</td>
      <td>$2286.33</td>
    </tr>
  </tbody>
</table>
</div>



## Gender Demographics


```python
gender = df.groupby('Gender')['SN'].nunique().reset_index()
gender = gender.rename(columns={'SN': 'Gender_Count'})
total_players = gender.Gender_Count.sum()
gender['Percentage of Players'] = (gender['Gender_Count']/total_players*100).map('{:.2f}%'.format)
gender
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
      <th>Gender</th>
      <th>Gender_Count</th>
      <th>Percentage of Players</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Female</td>
      <td>100</td>
      <td>17.45%</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Male</td>
      <td>465</td>
      <td>81.15%</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Other / Non-Disclosed</td>
      <td>8</td>
      <td>1.40%</td>
    </tr>
  </tbody>
</table>
</div>



## Purchasing Analysis (Gender)


```python
gender_purchase_analysis = df.groupby('Gender').Price.agg(['count', 'mean', 'sum']).reset_index()
gender_purchase_analysis = gender_purchase_analysis.rename(columns={'count': 'Purchase Count', 'mean': 'Avg Purchase Price','sum': 'Total Purchase Value'})
gender_purchase_analysis['Normalized'] = gender_purchase_analysis['Total Purchase Value'] / gender['Gender_Count']
gender_purchase_analysis['Avg Purchase Price'] = (gender_purchase_analysis['Avg Purchase Price']).map('${:.2f}'.format)
gender_purchase_analysis['Total Purchase Value'] = gender_purchase_analysis['Total Purchase Value'].map('${:,.2f}'.format)
gender_purchase_analysis['Normalized'] = (gender_purchase_analysis['Normalized']).map('${:.2f}'.format)
gender_purchase_analysis
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
      <th>Gender</th>
      <th>Purchase Count</th>
      <th>Avg Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Female</td>
      <td>136</td>
      <td>$2.82</td>
      <td>$382.91</td>
      <td>$3.83</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Male</td>
      <td>633</td>
      <td>$2.95</td>
      <td>$1,867.68</td>
      <td>$4.02</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Other / Non-Disclosed</td>
      <td>11</td>
      <td>$3.25</td>
      <td>$35.74</td>
      <td>$4.47</td>
    </tr>
  </tbody>
</table>
</div>



## Age Demographics


```python
bins = [6.9, 11.9, 16.9, 21.9, 26.9, 31.9, 36.9, 41.9, 99999]
age_labels = ['7-11', '12-16', '17-21', '22-26', '27-31', '32-36', '37-41', '42 and older']
df['age_labels'] = pd.cut(df['Age'], bins, labels=age_labels)
age_counts = (df.groupby("age_labels"))['SN'].nunique().reset_index()
age_counts = age_counts.rename(columns={'SN': 'Total Count'})
age_counts
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
      <th>age_labels</th>
      <th>Total Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>7-11</td>
      <td>27</td>
    </tr>
    <tr>
      <th>1</th>
      <td>12-16</td>
      <td>68</td>
    </tr>
    <tr>
      <th>2</th>
      <td>17-21</td>
      <td>154</td>
    </tr>
    <tr>
      <th>3</th>
      <td>22-26</td>
      <td>208</td>
    </tr>
    <tr>
      <th>4</th>
      <td>27-31</td>
      <td>54</td>
    </tr>
    <tr>
      <th>5</th>
      <td>32-36</td>
      <td>37</td>
    </tr>
    <tr>
      <th>6</th>
      <td>37-41</td>
      <td>22</td>
    </tr>
    <tr>
      <th>7</th>
      <td>42 and older</td>
      <td>3</td>
    </tr>
  </tbody>
</table>
</div>




```python
age_purchase_analysis = df.groupby('age_labels').Price.agg(['count', 'mean', 'sum']).reset_index()
age_purchase_analysis = age_purchase_analysis.rename(columns={'count': 'Purchase Count', 'mean': 'Avg Purchase Price','sum': 'Total Purchase Value'})
age_purchase_analysis['Normalized'] = age_purchase_analysis['Total Purchase Value'] / age_counts['Total Count']
age_purchase_analysis['Avg Purchase Price'] = (age_purchase_analysis['Avg Purchase Price']).map('${:.2f}'.format)
age_purchase_analysis['Total Purchase Value'] = (age_purchase_analysis['Total Purchase Value']).map('${:.2f}'.format)
age_purchase_analysis['Normalized'] = (age_purchase_analysis['Normalized']).map('${:.2f}'.format)
age_purchase_analysis
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
      <th>age_labels</th>
      <th>Purchase Count</th>
      <th>Avg Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>7-11</td>
      <td>41</td>
      <td>$3.01</td>
      <td>$123.38</td>
      <td>$4.57</td>
    </tr>
    <tr>
      <th>1</th>
      <td>12-16</td>
      <td>92</td>
      <td>$2.81</td>
      <td>$258.10</td>
      <td>$3.80</td>
    </tr>
    <tr>
      <th>2</th>
      <td>17-21</td>
      <td>204</td>
      <td>$2.88</td>
      <td>$588.40</td>
      <td>$3.82</td>
    </tr>
    <tr>
      <th>3</th>
      <td>22-26</td>
      <td>275</td>
      <td>$2.96</td>
      <td>$814.07</td>
      <td>$3.91</td>
    </tr>
    <tr>
      <th>4</th>
      <td>27-31</td>
      <td>79</td>
      <td>$2.98</td>
      <td>$235.61</td>
      <td>$4.36</td>
    </tr>
    <tr>
      <th>5</th>
      <td>32-36</td>
      <td>49</td>
      <td>$3.08</td>
      <td>$150.78</td>
      <td>$4.08</td>
    </tr>
    <tr>
      <th>6</th>
      <td>37-41</td>
      <td>37</td>
      <td>$2.90</td>
      <td>$107.35</td>
      <td>$4.88</td>
    </tr>
    <tr>
      <th>7</th>
      <td>42 and older</td>
      <td>3</td>
      <td>$2.88</td>
      <td>$8.64</td>
      <td>$2.88</td>
    </tr>
  </tbody>
</table>
</div>



## Top Spenders


```python
purchases_by_sn = df.groupby('SN').Price.agg(['count', 'mean', 'sum']).nlargest(5, 'sum').reset_index()
purchases_by_sn = purchases_by_sn.rename(columns={'count': 'Purchase Count', 'mean': 'Avg Purchase Price','sum': 'Total Purchase Value'})
purchases_by_sn['Avg Purchase Price'] = purchases_by_sn['Avg Purchase Price'].map('${:,.2f}'.format)
purchases_by_sn['Total Purchase Value'] = purchases_by_sn['Total Purchase Value'].map('${:,.2f}'.format)
purchases_by_sn
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
      <th>SN</th>
      <th>Purchase Count</th>
      <th>Avg Purchase Price</th>
      <th>Total Purchase Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Undirrala66</td>
      <td>5</td>
      <td>$3.41</td>
      <td>$17.06</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Saedue76</td>
      <td>4</td>
      <td>$3.39</td>
      <td>$13.56</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Mindimnya67</td>
      <td>4</td>
      <td>$3.18</td>
      <td>$12.74</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Haellysu29</td>
      <td>3</td>
      <td>$4.24</td>
      <td>$12.73</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Eoda93</td>
      <td>3</td>
      <td>$3.86</td>
      <td>$11.58</td>
    </tr>
  </tbody>
</table>
</div>



## Most Popular Items


```python
pop_items = df.groupby(['Item ID', 'Item Name', 'Price']).Price.agg(['count', 'sum']).nlargest(5, 'count').reset_index()
pop_items = pop_items.rename(columns={'count': 'Purchase Count', 'sum': 'Total Purchase Value'})
pop_items['Price'] = pop_items['Price'].map('${:,.2f}'.format)
pop_items['Total Purchase Value'] = pop_items['Total Purchase Value'].map('${:,.2f}'.format)
pop_items
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
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Price</th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>39</td>
      <td>Betrayal, Whisper of Grieving Widows</td>
      <td>$2.35</td>
      <td>11</td>
      <td>$25.85</td>
    </tr>
    <tr>
      <th>1</th>
      <td>84</td>
      <td>Arcane Gem</td>
      <td>$2.23</td>
      <td>11</td>
      <td>$24.53</td>
    </tr>
    <tr>
      <th>2</th>
      <td>13</td>
      <td>Serenity</td>
      <td>$1.49</td>
      <td>9</td>
      <td>$13.41</td>
    </tr>
    <tr>
      <th>3</th>
      <td>31</td>
      <td>Trickster</td>
      <td>$2.07</td>
      <td>9</td>
      <td>$18.63</td>
    </tr>
    <tr>
      <th>4</th>
      <td>34</td>
      <td>Retribution Axe</td>
      <td>$4.14</td>
      <td>9</td>
      <td>$37.26</td>
    </tr>
  </tbody>
</table>
</div>



## Most Profitable Items


```python
profitable_item = df.groupby(['Item ID', 'Item Name', 'Price']).Price.agg(['count', 'sum']).nlargest(5, 'sum').reset_index()
profitable_item = profitable_item.rename(columns={'count': 'Purchase Count', 'sum': 'Total Purchase Value'})
profitable_item['Total Purchase Value'] = profitable_item['Total Purchase Value'].map('${:,.2f}'.format)
profitable_item['Price'] = profitable_item['Price'].map('${:,.2f}'.format)
profitable_item
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
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Price</th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>34</td>
      <td>Retribution Axe</td>
      <td>$4.14</td>
      <td>9</td>
      <td>$37.26</td>
    </tr>
    <tr>
      <th>1</th>
      <td>115</td>
      <td>Spectral Diamond Doomblade</td>
      <td>$4.25</td>
      <td>7</td>
      <td>$29.75</td>
    </tr>
    <tr>
      <th>2</th>
      <td>32</td>
      <td>Orenmir</td>
      <td>$4.95</td>
      <td>6</td>
      <td>$29.70</td>
    </tr>
    <tr>
      <th>3</th>
      <td>103</td>
      <td>Singed Scalpel</td>
      <td>$4.87</td>
      <td>6</td>
      <td>$29.22</td>
    </tr>
    <tr>
      <th>4</th>
      <td>107</td>
      <td>Splitter, Foe Of Subtlety</td>
      <td>$3.61</td>
      <td>8</td>
      <td>$28.88</td>
    </tr>
  </tbody>
</table>
</div>



## Three Observable Trends

1: The majority (81%) of players/spenders are males. They spent $1,867.68, which was also 81 percent of the total.

2: In regard to ages, the majority of players are between the ages of 17 and 26, making up 63% of the total players. This age group also spends the most, spending $1402.47 which is more than half the total revenue. 

3: Retribution Axe was the top selling item, and also was one of the most purchased items. Its selling price is higher than the average item price, which is contributed to its high total purchase value. To take that a step further, all of the top five "profitable" items are priced higher than the average item price. 

Note: The use of 'profitable' is incorrectly used in this homework assignment. I would be careful in presenting something as profitable without factoring in cost.
