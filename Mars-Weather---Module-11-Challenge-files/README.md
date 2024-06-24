# Mars-Weather---Module-11-Challenge-files
This new assignment consists of two technical products. You will submit the following deliverables:  Deliverable 1: Scrape titles and preview text from Mars news articles.  Deliverable 2: Scrape and analyze Mars weather data, which exists in a table.

# Deliverable 1: Scrape Titles and Preview Text from Mars News
# Import Splinter and BeautifulSoup
from splinter import Browser
from bs4 import BeautifulSoup
import json

# Visit the Mars news site
url = 'https://static.bc-edx.com/data/web/mars_news/index.html'
browser.visit(url)

# Create a Beautiful Soup object
html = browser.html
soup = BeautifulSoup(html, 'html.parser')
soup

# Extract all the text elements
text_elements=soup.find_all('div',class_='list_text')
text_elements

# Create an empty list to store the dictionaries
news_article = []

# Loop through the text elements
# Extract the title and preview text from the elements
# Store each title and preview pair in a dictionary
# Add the dictionary to the list
for element in text_elements:
    dict_pair={}
    dict_pair['title'] = element.find_all('div', class_='content_title') [0].text
    dict_pair['preview'] = element.find_all('div', class_='article_teaser_body') [0].text
    news_article.append(dict_pair)

dict_pair

# Print the list to confirm success
news_article

# Optionally, store the scraped data in a file (to ease sharing the data with others). To do so, export the scraped data to a JSON file. 
# (Note: there will be no extra points for completing this.)
news_article_json =json.dumps(news_article)
with open('news_article_json', 'w') as file:
    file.write(news_article_json)

# Deliverable 2: Scrape and Analyze Mars Weather Data
# Import relevant libraries
from splinter import Browser
from bs4 import BeautifulSoup
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np

browser = Browser('chrome')

# Visit the website
# https://static.bc-edx.com/data/web/mars_facts/temperature.html
url = "https://static.bc-edx.com/data/web/mars_facts/temperature.html"
browser.visit(url)

# Create a Beautiful Soup Object
html = browser.html
soup = BeautifulSoup(html, 'html.parser')
soup

# Extract all rows of data
rows_of_data=soup.find_all('tr', class_='data-row')
rows_of_data

# Create an empty list
mars_temp_data = []
# Loop through the scraped data to create a list of rows
for row in rows_of_data:
    cell_values = row.find_all('td')
    values = []
    for value in cell_values:
        values.append(value.text)
    mars_temp_data.append(values)

mars_temp_data

# Create a Pandas DataFrame by using the list of rows and a list of the column names
mars_df = pd.DataFrame(mars_temp_data, columns = ['id', 'terrestrial_date', 'sol', 'ls', 'month', 'min_temp', 'pressure'])

# Confirm DataFrame was created successfully
mars_df

# Examine data type of each column
mars_df.dtypes

# Change data types for data analysis
mars_df["id"]= pd.to_numeric(mars_df["id"])
mars_df["terrestrial_date"] = pd.to_datetime(mars_df["terrestrial_date"])
mars_df["sol"] = pd.to_numeric(mars_df["sol"])
mars_df["ls"] = pd.to_numeric(mars_df["ls"])
mars_df["month"] = pd.to_numeric(mars_df["month"])
mars_df["min_temp"] = pd.to_numeric(mars_df["min_temp"])
mars_df["pressure"] = pd.to_numeric(mars_df["pressure"])

# Confirm type changes were successful by examining data types again
mars_df.dtypes

# 1. How many months are there on Mars?
mars_df['month'].max()

# 2. How many Martian days' worth of data are there?
martian_days = len(mars_df)
print(martian_days)

# 3. What is the average low temperature by month?
avg_temp = mars_df["min_temp"].groupby(mars_df['month']).mean()
avg_temp

avg_temp_by_month = mars_df.groupby('month')['min_temp'].mean().reset_index()
avg_temp_sorted = avg_temp_by_month.sort_values(by='min_temp')
print(avg_temp_sorted)

# Plot the average temperature by month
avg_temp.plot.bar(rot = 0)
plt.ylabel('Temperature (Celcius)')
print ('Average Temperature by Month')

# Identify the coldest and hottest months in Curiosity's location
temp_order=avg_temp.sort_values()
temp_order.plot.bar()
plt.ylabel('Temperature (Celcius)')
print('On average, the third month has the coldest minimum temperature on Mars, and the eighth month is the warmest.')

# 4. Average pressure by Martian month
avg_pressure_monthly = mars_df["pressure"].groupby(mars_df["month"]).mean()
avg_pressure_monthly

# Plot the average pressure by month
avg_pressure_monthly.plot.bar(rot=0)
plt.ylabel('Atmospheric Pressure')
print ('Atmospheric pressure is, on average, lowest in the sixth month and highest in the ninth.')

# 5. How many terrestrial (earth) days are there in a Martian year?
# The clusters of days are shown between 450 and 1125 which, delta being 675 of terrestrial days in a marian year.
plt.bar(x=mars_df.index, height=mars_df['min_temp'])
plt.ylabel('Temperature (Celcius)')
plt.xlabel('Number of Terrestrial Days')
plt.show()
print ('The clusters of days are shown between 450 and 1125. Delta being 675 of terrestrial days in a marian year. Internet search confirms that a Mars year is equivalent to 687 earth days.')

# Write the data to a CSV
mars_df.to_csv('Resources/mars_weather.csv', index = True)
