# Garment-Productivity
Garment manufacturing is a labor-intensive business and therefore the performance of the employees is a crucial factor in the achievement of production goals and financial success. Nevertheless, there are several factors that can affect the employee productivity such as absenteeism, idle time, incentives and overtime.


## Step 1 import all python libraries that will be required for my project work
import pandas as pd                                                        # for data frames
import numpy as np                                                       # for numerical computation
import matplotlib.pyplot as plt                                  # for data visualization
import seaborn as sns                                                  # for data visualization

### Step 2 load data set unto python
df_garments = pd.read_csv('C:/Users/acer.DESKTOP-LM3JJLR/Documents/business analytics/productivity+prediction+of+garment+employees/garments_worker_productivity.csv')


### NOW WE START BY CLEANING OUR DATASET TO PREVENT ANY ANOMALIES###

#### Step 1 Information about  the data types of each of the variable in our data set 
## Study book reference page 154
df_garments.info()

### Step 2 : check for missing values  
### Study book reference page 164
df_garments.isna().sum()


### Step 3: Convert the 'date' column to datetime format and ensure the correct format is applied
## Study book reference page 186
df_garments['date'] = pd.to_datetime(df_garments['date'], errors='coerce')

### Step 4 Calculate the accurate quarter based on the 'date' column applied 
###Study book reference page 316
df_garments['quarter'] = df_garments['date'].dt.to_period('Q')

#### After cleaning the data i realized our quarter variable has only quarter one so decided to  use the month instead of the quarter
# Step 5: Extract the full month name from the 'date' column 
###study book reference page 413 
df_garments['month'] = df_garments['date'].dt.strftime('%B')

# Step 6: Replace the 'quarter' column with the 'month' column 
### study book reference page 170 
df_garments['quarter'] = df_garments['month']

# ## Step 7: Then we delete the redundant month column since we no longer need it 
###study book reference page 170 
df_garments =df_garments.drop(columns=['month'])

### Step 8: Rename the 'quarter' column to 'month'   
###study book reference page 291
df_garments.rename(columns={'quarter': 'month'}, inplace=True)

### Step 9: Replace incorrect spelling in the 'department' column
 ###study book reference page 121
df_garments['department'] = df_garments['department'].replace('sweing', 'sewing')

### Step 10: Remove spaces between any spaces in the data that can distort the information 
### study book reference page 121
df_garments['department'] = df_garments['department'].str.strip()

### Step 11: Convert idle men and no of workers to integers, since we cannot have a decimal man 
### study book reference page 182
df_garments['idle_men'] = df_garments['idle_men'].astype(int)
df_garments['no_of_workers'] = df_garments['no_of_workers'].astype(int)


#### Step 11:  Calculate our outliers the using the Interquartile range

### calculating the 25% quantile and 75% quantile 
Q1 = df_garments[['targeted_productivity','smv','wip','over_time','incentive',
                  'idle_time','no_of_style_change','actual_productivity']].quantile(0.25)
Q3 = df_garments[['targeted_productivity','smv','wip','over_time','incentive',
                  'idle_time','no_of_style_change','actual_productivity']].quantile(0.75)
IQR = Q3 - Q1    #### Calculate the interquartile range

### to identify the lower and upper bound 
lower_bound = Q1 - 1.5 * IQR      ### minimum value to which a variable shouldn’t be lower
upper_bound = Q3 + 1.5 * IQR   ### maximum value to which a variable shouldn’t exceed














### DESCRPTIVE STATISTICS AND ANALYSIS ###

###Step 1: Select only numeric columns, therefore excluding 'team','date','month','department','day' 
###study book reference page 170 
numerical_columns = df_garments.drop(columns=['team','date','month','department','day'])

####Step 2: Create a summary matrix  
###study book reference page 170 
Summary_Stat = pd.DataFrame({
    'mean': numerical_columns.mean(),
    'median': numerical_columns.median(),
    'std': numerical_columns.std(),
    'max': numerical_columns.max(),
    'min': numerical_columns.min(),
    '25% (Q1)': numerical_columns.quantile(0.25),
    '75% (Q3)': numerical_columns.quantile(0.75)
})
print(Summary_Stat)     ###### to display the summary statistics



#######Step 3:  show the relationship between the numerical data we use the correlation matrix
### project reference Chapter 17 in class bikers
correlation_matrix = numerical_columns.corr()







### DATA VISUALIZATION####

#### Started by creating a categorical-categorical relationship ####

####Show the relationship between the month and the department  
####study book reference page 204 
##Step 1: created a pivot table to summarize the relationship easier
M_Depart = df_garments.pivot_table(values='date', index='month', 
                                  columns='department', aggfunc='count')

## Step2:  Created a list for the month to be rearranged accurately.
desired_order = ['January', 'February', 'March']

## Step3:  Reindex the pivot  data frame to reorder the month rows
M_Departm  = M_Depart .reindex(desired_order)



## Step4: Show a graphical representation in a bar chat
M_Departm.plot(kind='bar', figsize=(8, 5))           
plt.title("Departmental Activity Counts Across Month")       
plt.xlabel("Month")
plt.ylabel("Count")
plt.show()



#### Secondly created  a categorical-numerical relationship ####
#### Study book reference page 280

#### 1)Relationship between Month and the actual productivity 
##Step 1: created a pivot table to summarize the relationship easier
M_AP = df_garments.pivot_table(values='actual_productivity',
                                                index='month', aggfunc='mean')
## Step2:  Reindex the pivot  data frame to reorder the month rows
M_ap = M_AP.reindex(desired_order)

## Step3: Show a graphical representation in a line chat
M_ap.plot()
plt.title("Analysis of Actual Productivity by Month")
plt.xlabel("Month")
plt.ylabel("Percentage")
plt.show()






#### 2 Relationship between Month and the actual productivity and targeted productivity
##Step 1: created a pivot table to summarize the relationship easier
M_APT = df_garments.pivot_table(values=['actual_productivity','targeted_productivity'],
                                                index='month', aggfunc='mean')

## Step2:  Reindex the pivot  data frame to reorder the month rows
M_apt = M_APT.reindex(desired_order)

## Step3: Show a graphical representation in a line chat
M_apt.plot()
plt.title("Analysis of Actual and Targeted Productivity by Month")
plt.xlabel("Month")
plt.ylabel("Percentage")
plt.show()

#### 3 Relationship between Day and the actual productivity 
## Step 1:  Created a list for the month to be rearranged accurately.
Accurate_day =['Monday','Tuesday','Wednesday','Thursday','Saturday','Sunday']
##Step 2: created a pivot table to summarize the relationship easier
M_APD = df_garments.pivot_table(values=['actual_productivity'],
                                                index='day', aggfunc='mean')
## Step 3:  Reindex the pivot  data frame to reorder the month rows
M_apd = M_APD.reindex(Accurate_day)

## Step 4: Show a graphical representation in a line chat
plt.title("Analysis of Actual Productivity by day")
M_apd.plot(color='teal')
plt.xlabel("Day")
plt.ylabel("Percentage")
plt.show()



#### Box plot for productivity by team 

Show a graphical representation in a box plot
plt.figure(figsize=(10, 6))
sns.boxplot(x=df_garments.team, y=df_garments.actual_productivity, color='teal')
plt.title('Productivity by Team', fontsize=14)
plt.xlabel('Team')
plt.ylabel('Productivity Percentage')
plt.grid(True)



#### Finaly  numerical vrs numerical relationship

# Scatter plot for Number of Workers vs. SMV
plt.figure(figsize=(10, 6))
sns.scatterplot(x=df_garments.no_of_workers, y=df_garments.smv, color='teal')
plt.title('Number of Workers vs SMV', fontsize=14)
plt.xlabel('Number of Workers')
plt.ylabel('SMV')
plt.grid(True)






# Scatter plot for Target Productivity vs. Actual Productivity
plt.figure(figsize=(10, 6))
sns.scatterplot(x=df_garments.actual_productivity, y=df_garments.targeted_productivity, color='teal')
plt.title('Target_productivity vs. Actual Productivity', fontsize=14)
plt.xlabel('Actual Productivity')
plt.ylabel('Targeted productivity')
plt.grid(True)

# Scatter plot for Number of Workers vs Overtime
plt.figure(figsize=(10, 6))
sns.scatterplot(x=df_garments.no_of_workers, y=df_garments.over_time, color='teal')
plt.title('Number of Workers vs. Over Time', fontsize=14)
plt.xlabel('Number of Workers')
plt.ylabel('Over Time')
plt.grid(True)




# Scatter plot for SMV vs Overtime

plt.figure(figsize=(10, 6))
sns.scatterplot(x=df_garments.smv, y=df_garments.over_time, color='teal')
plt.title('SMV vs. Over Time', fontsize=14)
plt.xlabel('SMV')
plt.ylabel('Over Time')
plt.grid(True)



# Scatter plot for idle men vs idle time

plt.figure(figsize=(10, 6))
sns.scatterplot(x=df_garments.idle_time, y=df_garments.idle_men, color='teal')
plt.title('idle time vs. idle men', fontsize=14)
plt.xlabel('idle time')
plt.ylabel('idle men')
plt.grid(True)



# Scatter plot for Actual productivity  vs Incentive


plt.figure(figsize=(10, 6))
sns.scatterplot(x=df_garments.actual_productivity, y=df_garments.incentive, color='teal')
plt.title('idle time vs. idle men', fontsize=14)
plt.xlabel('idle time')
plt.ylabel('idle men')
plt.grid(True)
