## Data Dictionary:
<img width="748" height="485" alt="image" src="https://github.com/user-attachments/assets/e64df6ce-fc27-44b5-80e1-b53e49c199ef" />


## Problem 1:
> What percentage of all **licensed** sets ever released were Star Wars themed?

- First, the data is imported into dataframes.
- The data is validated in preparation for analysis.
```python
import pandas as pd

lego_sets = pd.read_csv('data/lego_sets.csv')

lego_sets = lego_sets.dropna(subset = ['set_num', 'name', 'theme_name'])

parent_themes = pd.read_csv('data/parent_themes.csv')
```
- The themes dataframe is narrowed down to specifically **licensed** themes.
```python
licensed_themes = parent_themes[parent_themes['is_licensed'] == True]

licensed_themes = licensed_themes.reset_index().drop('index', axis=1)
```
- Then, the sets dataframe is narrowed down to only themes present in the licensed_themes dataframe.
```python
licensed_sets = lego_sets[lego_sets['parent_theme'].isin(licensed_themes['name'])]
```
- star_wars represents only rows from licensed_sets where the theme equals 'Star Wars.'
```python
star_wars = licensed_sets[licensed_sets['parent_theme'] == 'Star Wars']
```
- Finally, the_force represents the percentage of all licensed sets that were Star Wars themed.
```python
the_force = int(len(star_wars)/len(licensed_sets) * 100)

print("Percentage of all licensed sets ever released that were Star Wars themed: ", the_force, "%")
```
## Output:
<img width="563" height="19" alt="image" src="https://github.com/user-attachments/assets/170bb323-f4f8-4532-85b2-ed94f0cafef0" />

## Problem 2:
> In which year was the highest number of Star Wars sets released?

- new_era represents the year with highest number of Star Wars sets released. The star_wars dataframe is grouped according to listed year, a count of 'set_num' unique identifier is taken, and the index for the max value in the dataframe is printed as the result.
```python
new_era = star_wars.groupby('year')['set_num'].count().idxmax()

print("Year with highest number of Star Wars sets released: ", new_era)
```
## Output:
<img width="412" height="16" alt="image" src="https://github.com/user-attachments/assets/c2a71333-884f-444e-8041-23bfb367be2c" />
