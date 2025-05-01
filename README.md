# Uber-Rides-Project-Python
The Uber Rides Data Analytics Using Python this project is aimed to analyze the insights from given dataset for the more usage of vechicles in Uber App


# Importing Libraries

* To importing all these libraries, we can use the  below code :

```
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
```


# Importing Dataset

* we can import the dataset using the pandas library.

```
dataset = pd.read_csv("UberDataset.csv")
dataset.head()
```

Output:

![image alt](https://github.com/ChandraHari05/Uber-Rides-Project-Python/blob/222cbb2a4a9a8f8dbe0545282234807487c29795/project_details/Screenshots/dataset_head.png)

To find the shape of the dataset, we can use dataset.shape

```
dataset.shape
```

Output:

![image alt](https://github.com/ChandraHari05/Uber-Rides-Project-Python/blob/222cbb2a4a9a8f8dbe0545282234807487c29795/project_details/Screenshots/dataset_info.png)

To know about the null values count, datatype, etc. So for that we will use the below code.

```
dataset.info()
```

Output:

![image_alt](https://github.com/ChandraHari05/Uber-Rides-Project-Python/blob/e2e37e0f214d4696386091718ea56c858c021402/project_details/Screenshots/dataset_shape.png)

# Data Preprocessing

we understood that there are a lot of null values in PURPOSE column, so for that we will me filling the null values with a NOT keyword. You can try something else too.

```
dataset['PURPOSE'].fillna("NOT", inplace=True)
```

Changing the START_DATE and END_DATE to the date_time format so that further it can be use to do analysis.

```
dataset['START_DATE'] = pd.to_datetime(dataset['START_DATE'], 
                                       errors='coerce')
dataset['END_DATE'] = pd.to_datetime(dataset['END_DATE'], 
                                     errors='coerce')
```

Splitting the START_DATE to date and time column and then converting the time into four different categories i.e. Morning, Afternoon, Evening, Night

```
from datetime import datetime

dataset['date'] = pd.DatetimeIndex(dataset['START_DATE']).date
dataset['time'] = pd.DatetimeIndex(dataset['START_DATE']).hour

#changing into categories of day and night
dataset['day-night'] = pd.cut(x=dataset['time'],
                              bins = [0,10,15,19,24],
                              labels = ['Morning','Afternoon','Evening','Night'])
```

Once we are done with creating new columns, we can now drop rows with null values.

```
dataset.dropna(inplace=True)
```

It is also important to drop the duplicates rows from the dataset. To do that, refer the code below.

```
dataset.drop_duplicates(inplace=True)
```

# Data Visualization

Start checking the unique values in dataset of the columns with object datatype.

```
obj = (dataset.dtypes == 'object')
object_cols = list(obj[obj].index)

unique_values = {}
for col in object_cols:
  unique_values[col] = dataset[col].unique().size
unique_values
```

Output:

![image_alt](https://github.com/ChandraHari05/Uber-Rides-Project-Python/blob/3371a04b7837be252fad02227354fc5fb386f8b0/project_details/Screenshots/dataset_visual.png)

we will be using matplotlib and seaborn library for countplot the CATEGORY and PURPOSE columns.

```
plt.figure(figsize=(10,5))

plt.subplot(1,2,1)
sns.countplot(dataset['CATEGORY'])
plt.xticks(rotation=90)

plt.subplot(1,2,2)
sns.countplot(dataset['PURPOSE'])
plt.xticks(rotation=90)
```

Output:

![image_alt](https://github.com/ChandraHari05/Uber-Rides-Project-Python/blob/c1e9cdda60645f46aedb54c39fed447cd411301c/project_details/Visualisations/countplot.jpg.png)

We do the same for time column, here we will be using the time column which we have extracted above.

```
sns.countplot(dataset['day-night'])
plt.xticks(rotation=90)
```

Output:

![image_alt](https://github.com/ChandraHari05/Uber-Rides-Project-Python/blob/0bcaff40fde27682a0e8e4c9ed81aad35e8609a6/project_details/Visualisations/countplot_day%26night.jpg.png)

we will be comparing the two different categories along with the PURPOSE of the user.

```
plt.figure(figsize=(15, 5))
sns.countplot(data=dataset, x='PURPOSE', hue='CATEGORY')
plt.xticks(rotation=90)
plt.show()
```

Output:

![image_alt](https://github.com/ChandraHari05/Uber-Rides-Project-Python/blob/8d8da605849191f565068fd351e649a970f26462/project_details/Visualisations/countplot%20purpose%20%26%20cato.jpg.png)

# The above count-plots shows :

___Most of the rides are booked for business purpose.___

___Most of the people book cabs for Meetings and Meal / Entertain purpose.___

___Most of the cabs are booked in the time duration of 10am-5pm (Afternoon).___

**we can now find the correlation between the columns using heatmap.**

```
numeric_dataset = dataset.select_dtypes(include=['number'])
sns.heatmap(numeric_dataset.corr(), 
            cmap='BrBG', 
            fmt='.2f', 
            linewidths=2, 
            annot=True)
```

Output:

![image_alt](https://github.com/ChandraHari05/Uber-Rides-Project-Python/blob/ebe81e87b04f5ec12ddc77cd6efc00ee71be20ad/project_details/Visualisations/heatmao.jpg.png)

___Insights from the heatmap:___

**Business and Personal Category are highly negatively correlated, this have already proven earlier. So this plot, justifies the above conclusions.**
 
**There is not much correlation between the features.**

___We need to visualize the month data. This can we same as done before (for hours).___

```
dataset['MONTH'] = pd.DatetimeIndex(dataset['START_DATE']).month
month_label = {1.0: 'Jan', 2.0: 'Feb', 3.0: 'Mar', 4.0: 'April',
               5.0: 'May', 6.0: 'June', 7.0: 'July', 8.0: 'Aug',
               9.0: 'Sep', 10.0: 'Oct', 11.0: 'Nov', 12.0: 'Dec'}
dataset["MONTH"] = dataset.MONTH.map(month_label)

mon = dataset.MONTH.value_counts(sort=False)

# Month total rides count vs Month ride max count
df = pd.DataFrame({"MONTHS": mon.values,
                   "VALUE COUNT": dataset.groupby('MONTH',
                                                  sort=False)['MILES'].max()})

p = sns.lineplot(data=df)
p.set(xlabel="MONTHS", ylabel="VALUE COUNT")
```

Output:

![image_alt](https://github.com/ChandraHari05/Uber-Rides-Project-Python/blob/8036545473e9690aa03ce164e46f52539e9f5dee/project_details/Visualisations/lineplot.jpg.png)

___Insights from the above plot :___

**The counts are very irregular.**

**Still its very clear that the counts are very less during Nov, Dec, Jan, which justifies the fact that  time winters are there in Florida, US.**

Visualization for days data.

```
dataset['DAY'] = dataset.START_DATE.dt.weekday
day_label = {
    0: 'Mon', 1: 'Tues', 2: 'Wed', 3: 'Thus', 4: 'Fri', 5: 'Sat', 6: 'Sun'
}
dataset['DAY'] = dataset['DAY'].map(day_label)
```
```
day_label = dataset.DAY.value_counts()
sns.barplot(x=day_label.index, y=day_label);
plt.xlabel('DAY')
plt.ylabel('COUNT')
```

Output:

![image_alt](https://github.com/ChandraHari05/Uber-Rides-Project-Python/blob/85c1addcbf11b519ef9de9833ed6c51931b8aff8/project_details/Visualisations/barplot.jpg.png)

We can use boxplot to check the distribution of the column.[MILES]

```
sns.boxplot(dataset['MILES'])
```

Output:

![image_alt](https://github.com/ChandraHari05/Uber-Rides-Project-Python/blob/d0cdc67297c7b7f00cf5f8f373c60f40596e11d0/project_details/Visualisations/boxplot.jpg.png)

As the graph is not clearly understandable. Let’s zoom in it for values lees than 100.

```
sns.boxplot(dataset[dataset['MILES']<100]['MILES'])
```

Output:

![image_alt](https://github.com/ChandraHari05/Uber-Rides-Project-Python/blob/dbbd73b35a14be3b837789eb1fcef20e677fb230/project_details/Visualisations/boxplot_less%20than%2010.jpg.png)

It’s bit visible. But to get more clarity we can use distplot for values less than 40.

```
sns.histplot(dataset[dataset['MILES']<40]['MILES'])
```

Output:

![image_alt](https://github.com/ChandraHari05/Uber-Rides-Project-Python/blob/46ee7ace6a9ff631450436a05f65cee9b23c003f/project_details/Visualisations/histoplot.jpg.png)

___Insights from the above plots :___

**Most of the cabs booked for the distance of 4-5 miles.**

**Majorly people chooses cabs for the distance of 0-20 miles.**

**For distance more than 20 miles cab counts is nearly negligible.**

<center>THANK YOU</center>
