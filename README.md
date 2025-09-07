STC TV Code Analysis - For User

Programmatic code extracted from Jupyter Notebook for analyzing viewing patterns and viewer categories between HD and SD.

1. Environment Setup and Google Drive Connection

This section includes connecting Google Drive to Colab and specifying the main working directory.

Python


import os
Root = "/content/drive/MyDrive/Colab Notebooks"
os.chdir(Root)
from google.colab import drive
drive.mount("/content/drive")


2. Install Required Libraries

Install the pyxlsb library necessary for reading Excel files in .xlsb format.

Python


"""
Install necessary libraries that are not installed by default
Example: pyxlsb
You can add any library you plan to use.
"""
!pip install pyxlsb


3. Import Libraries

Import all libraries necessary for data analysis and creating visualizations.

Python


# Import required libraries
"""
Please import any required libraries based on your needs
"""
import pandas as pd # Provides high-performance, easy-to-use data structures and data analysis tools
import pyxlsb # For reading Excel files with xlsb extension (input file)
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from matplotlib.ticker import FuncFormatter
import os # Provides fast mathematical computations on arrays and matrices


4. Data Reading and Initial Exploration

Read the data file and display basic information about the data structure.

Python


# Read Jawwy data set
dataframe = pd.read_excel("stc TV Data Set_T1.xlsb",
sheet_name="Final_Dataset")
# Please make a copy of the dataset if you will work directly and make changes to
# the dataset
#df=dataframe.copy()
# Check data shape
dataframe.shape
# Display first 5 rows of data
dataframe.head()


5. Initial Data Processing

Clean data, standardize column values, and convert data types.

Python


# Initial data processing
print("Processing data...")
if 'Column1' in dataframe.columns:
dataframe = dataframe.drop(columns=['Column1'])
dataframe['program_name'] = dataframe['program_name'].str.strip()
# Standardize program_class column values
print("Standardizing program_class column values...")
# Convert SERIES/EPISODES to SERIES
dataframe['program_class'] = dataframe['program_class'].replace('SERIES/\nEPISODES', 'SERIES')
print(f"Unique values in program_class column after standardization:\n{dataframe['program_class'].unique()}")
# Print data information
print("Data information:")
print(f"Data columns: {dataframe.columns.tolist()}")
print(f"Data types: {dataframe.dtypes}")
print(f"Number of missing values: {dataframe.isnull().sum().sum()}")


Python


# Convert numeric columns
dataframe[['duration_seconds', 'season', 'episode', 'series_title', 'hd']]
= dataframe[['duration_seconds', 'season', 'episode', 'series_title',
'hd']].apply(pd.to_numeric)
dataframe[['user_id_maped', 'program_name', 'program_class', 'program_desc',
'program_genre', 'original_name']] = dataframe[['user_id_maped',
'program_name', 'program_class', 'program_desc', 'program_genre',
'original_name']].astype(str)


6. Part One: Analyzing Viewer Categories by Program Type

Analyze user distribution and categorize them based on their preferences for series and movies.

Python


# Part One: Categorizing and analyzing viewer categories by program type
print("\n=== Part One: Categorizing and analyzing viewer categories by program type
===\n")
# Analyze user distribution by program type
print("Analyzing user distribution by program type...")
# Number of unique users
unique_users = dataframe['user_id_maped'].nunique()
print(f"Total number of unique users: {unique_users}")
# Number of users who watch movies
movie_users = set(dataframe[dataframe['program_class'] == 'MOVIE']
['user_id_maped'].unique())
print(f"Number of users who watch movies: {len(movie_users)}")
# Number of users who watch series
series_users = set(dataframe[dataframe['program_class'] == 'SERIES']
['user_id_maped'].unique())
print(f"Number of users who watch series: {len(series_users)}")


Python


# Number of users who watch movies only
movie_only_users = len(movie_users - series_users)
print(f"Number of users who watch movies only: {movie_only_users}")
# Number of users who watch series only
series_only_users = len(series_users - movie_users)
print(f"Number of users who watch series only: {series_only_users}")
# Number of users who watch both types
both_users = len(movie_users.intersection(series_users))
print(f"Number of users who watch both types: {both_users}")


7. Analyzing Watch Time

Analyze the total and average watch time for each program type.

Python


# Analyze watch time by program type
print("\nAnalyzing watch time by program type...")
# Total watch time for each type
total_duration_by_class = dataframe.groupby('program_class')
['duration_seconds'].sum()
print(f"Total watch time by program type (in seconds):\n{total_duration_by_class}")
# Average watch time for each type
avg_duration_by_class = dataframe.groupby('program_class')
['duration_seconds'].mean()
print(f"Average watch time by program type (in seconds):\n{avg_duration_by_class}")


8. Analyzing Genre Preferences

Analyze the most watched genres for movies and series.

Python


# Analyze genre preferences by program type
print("\nAnalyzing genre preferences by program type...")
# Most watched genres for movies
movie_genres = dataframe[dataframe['program_class'] ==
'MOVIE'].groupby('program_genre').size().sort_values(ascending=False)
print(f"Most watched genres for movies:\n{movie_genres.head(10)}")
# Most watched genres for series
series_genres = dataframe[dataframe['program_class'] ==
'SERIES'].groupby('program_genre').size().sort_values(ascending=False)
print(f"Most watched genres for series:\n{series_genres.head(10) if not\nseries_genres.empty else 'No sufficient data for series'}")


9. Part Two: Analyzing Viewing Patterns by Quality

Study viewing patterns between High Definition (HD) and Standard Definition (SD) quality.

Python


# Part Two: Studying different viewing patterns for users between High Quality
# (HD) and Standard Quality (SD)
print("\n=== Part Two: Studying different viewing patterns for users between High Quality
(HD) and Standard Quality (SD) ===\n")
# Analyze viewing patterns by display quality (HD and SD)
print("Analyzing viewing patterns by display quality...")
# Number of views in Standard Quality (SD) and High Quality (HD)
hd_counts = dataframe['hd'].value_counts()
print(f"Number of views by display quality:\n{hd_counts}")
# Percentage of views in Standard Quality (SD) and High Quality (HD)
hd_percentage = dataframe['hd'].value_counts(normalize=True) * 100
print(f"Percentage of views by display quality:\n{hd_percentage}")
# Analyze display quality by program type
hd_by_program_class = pd.crosstab(dataframe['program_class'],
dataframe['hd'])
print(f"Number of views by display quality and program type:\n{hd_by_program_class}")
# Percentage of display quality by program type
hd_by_program_class_percentage = pd.crosstab(dataframe['program_class'],
dataframe['hd'], normalize='index') * 100
print(f"Percentage of views by display quality and program type (%):\n{hd_by_program_class_percentage}")


Python


# Analyze display quality by program genre
hd_by_genre = pd.crosstab(dataframe['program_genre'],
dataframe['hd']).sort_values(by=1, ascending=False)
print(f"Number of views by program genre and display quality (top 10 genres in High
Quality):\n{hd_by_genre.head(10)}")


10. Creating Visualizations

Create a set of visualizations to illustrate results and trends in the data.

Python


# Create visualizations
print("\nCreating visualizations...")
# 1. Bar chart for user distribution by program type
plt.figure(figsize=(10, 6))
user_distribution = [movie_only_users, series_only_users, both_users]
labels = ['Movies Only', 'Series Only', 'Both Types']
# Check for non-zero values before plotting
if sum(user_distribution) > 0:
plt.pie(user_distribution, labels=labels, autopct='%1.1f%%',
startangle=90, colors=['#ff9999','#66b3ff','#99ff99'])
plt.title('User Distribution by Program Type Preferences')
plt.axis('equal')
plt.savefig('user_distribution_by_program_class.png', dpi=300,
bbox_inches='tight')
else:
print("Skipping user distribution plot due to insufficient data")
plt.close()


Python


# 2. Bar chart for total watch time by program type
plt.figure(figsize=(10, 6))
total_duration_by_class.plot(kind='bar', color=['#ff9999', '#66b3ff'])
plt.title('Total Watch Time by Program Type')
plt.xlabel('Program Type')
plt.ylabel('Total Watch Time (in seconds)')
plt.xticks(rotation=0)
plt.savefig('total_duration_by_program_class.png', dpi=300,
bbox_inches='tight')
plt.close()
# 3. Bar chart for most watched movie genres
plt.figure(figsize=(12, 6))
if not movie_genres.empty:
movie_genres.head(10).plot(kind='bar', color='#ff9999')
plt.title('Most Watched Movie Genres')
plt.xlabel('Movie Genre')
plt.ylabel('Number of Views')
plt.xticks(rotation=45, ha='right')
plt.tight_layout()
plt.savefig('top_movie_genres.png', dpi=300, bbox_inches='tight')
else:
print("Skipping most watched movie genres plot due to insufficient data")
plt.close()


11. More Visualizations

Additional visualizations for series analysis and display quality.

Python


# 4. Bar chart for most watched series genres
plt.figure(figsize=(12, 6))
if not series_genres.empty and len(series_genres) > 0:
series_genres.head(10).plot(kind='bar', color='#66b3ff')
plt.title('Most Watched Series Genres')
plt.xlabel('Series Genre')
plt.ylabel('Number of Views')
plt.xticks(rotation=45, ha='right')
plt.tight_layout()
plt.savefig('top_series_genres.png', dpi=300, bbox_inches='tight')
else:
print("Skipping most watched series genres plot due to insufficient data")
plt.close()


Python


# 5. Pie chart for viewing quality distribution
plt.figure(figsize=(8, 6))
hd_counts.plot(kind='pie', autopct='%1.1f%%', startangle=90,
colors=['#ff9999','#66b3ff'])
plt.title('Viewing Quality Distribution')
plt.ylabel('')
plt.legend(['SD', 'HD'])
plt.axis('equal')
plt.savefig('viewing_quality_distribution.png', dpi=300,
bbox_inches='tight')
plt.close()
# 6. Bar chart for display quality percentage by program type
plt.figure(figsize=(10, 6))
hd_by_program_class_percentage.plot(kind='bar', stacked=True,
color=['#ff9999','#66b3ff'])
plt.title('Display Quality Percentage by Program Type')
plt.xlabel('Program Type')
plt.ylabel('Percentage')
plt.xticks(rotation=0)
plt.legend(['SD', 'HD'])
plt.savefig('quality_by_program_class.png', dpi=300, bbox_inches='tight')
plt.close()


Python


# 7. Bar chart for most watched genres in High Quality
plt.figure(figsize=(12, 6))
if 1 in hd_by_genre.columns and not hd_by_genre.empty:
hd_by_genre.head(10)[1].plot(kind='bar', color='#66b3ff')
plt.title('Most Watched Genres in High Quality (HD)')
plt.xlabel('Program Genre')
plt.ylabel('Number of Views')
plt.xticks(rotation=45, ha='right')
plt.tight_layout()
plt.savefig('top_hd_genres.png', dpi=300, bbox_inches='tight')
else:
print("Skipping most watched genres in High Quality plot due to insufficient
data")
plt.close()


12. Analysis Completion

Confirming completion of all steps and printing analysis completion message.

Python


# Write analysis report
print("\nWriting analysis report...")
# Print report in co-lab
print("\n" + "="*50 + "\n")
print("Analysis completed and report written successfully!")
print("\n" + "="*50 + "\n")


Important Notes

Instructions for Use:

1.Make sure to upload the data file "stc TV Data Set_T1.xlsb" to the Colab folder.

2.Run the cells in the displayed order.

3.You will get a comprehensive analysis of user behavior and illustrative visualizations.

4. Variables and paths can be modified according to your needs.

Expected Outputs:

•
Detailed analysis of viewer categories

•
Visualizations of user distribution

•
Analysis of viewing patterns by quality

•
Comprehensive statistics on user behavior




