# SHARK ATTACK PROJECT

## OBJECTIVE

The main objective of this project is to analyze a database of shark attacks and be able to confirm or deny different hypotheses:

**1. Mortality from shark attacks has decreased in the last 50 years.**

**2. Surfing is the activity with the most provoked shark attacks.**

**3. The summer months in the United States (June to September) and Australia (December to March) are the most dangerous for surfing.**

**4. Most of the attacks are against people between 20 and 40 years old.**

## CLEANING PROCESS

At first, those columns that were not going to be necessary for the analysis, the rows that did not contain at least two valid values and the duplicates were eliminated:

<img src="/images/df.png">

The remaining columns were then cleaned individually.

**CASE NUMBER**

In the case of the 'Case Number' column, it was used to obtain the month of the year in which the attack occurred.

To extract it, regex was used and then added to a new column, changing the number to the name of the month.

```python
df['Month'] = df['Case Number'].str.extract('\.(\d{2})\.')

month = [['01','January'], ['02','February'], ['03','March'], ['04','April'], ['05','May'], ['06','June'], 
        ['07','July'], ['08','August'], ['09', 'September'], ['10', 'October'], ['11', 'November'], ['12', 'December']]

for x, y in month:
    df['Month'] = df['Month'].str.replace( x, y, regex = True)
```

**YEAR**

To clean up the 'Year' column, it was used to group them in decades from the year 1900 to make the visualizations more understandable.

```python
df["Decade"] = pd.cut(df['Year'], bins=[1899, 1910, 1920, 1930, 1940, 1950, 1960, 1970, 1980, 1990, 2000, 2010, 2020],
                labels=['1900 - 1910', '1910 - 1920', '1920 - 1930', '1930 - 1940','1940 - 1950', '1950 - 1960', 
                            '1960 - 1970', '1970 - 1980', '1980 - 1990', '1990 - 2000', '2000 - 2010', '2010 - 2020'])
```

**TYPE**

The 'Type' column was pretty clean, so it was only necessary to replace the invalid values and add some to the 'Boat' type.

```python
df['Type'].replace('Invalid', np.nan, inplace=True)
df['Type'].replace('Boating', 'Boat', inplace=True)
df['Type'].replace('Boatomg', 'Boat', inplace=True)
```

**COUNTRY**

In the case of the 'Country' column, the cleaning focused only on the United States and Australia, since they are the only ones that have been used in the analysis.

```python
df['Country'] = df['Country'].str.lower()

country = [['usa','united states'], ['aus','australia']]

for match, new in country:
    df['Country'] = df['Country'].str.replace('(^.*'+match+'.*$)', new, regex = True)

df['Country'] = df['Country'].str.title()
```

**ACTIVITY**

Very similar to the 'Country' column, in the 'Activity' column regular expression was used to match all those related activities into 4 big groups.

```python
df['Activity'] = df['Activity'].str.lower() 

act = [['surf','surfing'], ['board','surfing'], ['swim','swimming'], ['bath','swimming'], ['div','swimming'], ['snork','swimming'], 
        ['fish','fishing'], ['wad','wading'], ['stand', 'wading'], ['walk', 'wading']]

for match, new in act:
    df['Activity'] = df['Activity'].str.replace('(^.*'+match+'.*$)', new, regex = True)

act_2 = ['swimming','surfing','fishing','wading']
for i in df['Activity']:
    if i not in act_2:
        df['Activity'].replace(i,'other', inplace= True)

df['Activity'] = df['Activity'].str.capitalize()
```

**SEX**

In the 'Sex' column, a simple lambda function was used to group the values into 2 unique categories.

```python
df['Sex'] = df['Sex'].str.upper().str.strip()
df['Sex'] = df['Sex'].apply(lambda x: x if x in ('M','F') else np.nan)
```

**AGE**

To clean the 'Age' column, only numeric values were extracted using regex.

```python
df['Age'] = df['Age'].str.extract('(\d+)')
```

**FATAL (Y/N)**

In the case of the 'Fatal' column, the same logic was used as in the 'Sex' column.

```python
df['Fatal (Y/N)'] = df['Fatal (Y/N)'].str.upper().str.strip()
df['Fatal (Y/N)'] = df['Fatal (Y/N)'].apply(lambda x: x if x in ('Y','N') else np.nan)
```

**SPECIES**

Finally, in the case of the 'Species' column, very similar logic to the 'Activity' column was used.

```python
df['Activity'] = df['Activity'].str.lower() 

act = [['surf','surfing'], ['board','surfing'], ['swim','swimming'], ['bath','swimming'], ['div','swimming'], ['snork','swimming'], 
        ['fish','fishing'], ['wad','wading'], ['stand', 'wading'], ['walk', 'wading']]

for match, new in act:
    df['Activity'] = df['Activity'].str.replace('(^.*'+match+'.*$)', new, regex = True)

act_2 = ['swimming','surfing','fishing','wading']
for i in df['Activity']:
    if i not in act_2:
        df['Activity'].replace(i,'other', inplace= True)

df['Activity'] = df['Activity'].str.capitalize()
```

## ANALYSING AND HYPOTHESES

**1. Mortality from shark attacks has decreased in the last 50 years.**

Although mortality began to decrease a lot since 1960, between 1990 and 2000 it increased again. Therefore, although it is true that mortality is lower than 50 years ago, the minimum number of registered deaths was between 1980 and 1990.

<img src="/images/graph_1.png">

**2. Surfing is the activity with the most provoked shark attacks.**

If we look at the activities with the most attacks, we can see that swimming and surfing have the highest number of recorded attacks.

<img src="/images/graph_2.png">

But if we focus only on provoked attacks, we see that more than 50% of these attacks are on people who were fishing.

<img src="/images/graph_3.png">

**3. The summer months in the United States (June to September) and Australia (December to March) are the most dangerous for surfing.**

In the case of the United States, we see that the 4 months with the most shark attacks on surfers are July, August, September and October. Therefore, although October is not considered summer, the summer months are some of the months with the highest number of attacks on surfers.

<img src="/images/graph_4.png">

In the case of Australia, along with October, the 4 summer months are the ones that have recorded the most shark attacks on surfers.

<img src="/images/graph_5.png">

**4. Most of the attacks are against people between 20 and 40 years old.**

In the following boxplot it can be seen that 50% of the attacks are carried out on people between the ages of 18 and 35 approximately. So while it's not exactly 20 to 40 years old, it's not far off the mark.

<img src="/images/graph_6.png">

In case of taking into account the different species of sharks, although there are differences between species such as the Wobbegong or the Mako shark, they are not very significant.

<img src="/images/graph_7.png">