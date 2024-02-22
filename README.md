# Condo data web scraping project
## Introduction
This project uses BeautifulSoup and Selenium to scrape rental condo data from the website for January 2024.

## Python Libraries
### Install

```
pip install beautifulsoup4
pip install selenium
pip install selenium-stealth
```

### Import necessary library

```
from bs4 import BeautifulSoup
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.common.by import By
from selenium.webdriver.support import expected_conditions as EC
from selenium_stealth import stealth
import time
import pandas as pd
```


## Web Scraping Process
The reason I chose to use BS4 with Selenium is that the website I want to scrape doesn't allow scraping directly using `requests.get()` It returns error code 400. Therefore, I do it in this way.

### To run headless
```
options = webdriver.ChromeOptions()
options.add_argument("start-maximized")
options.add_argument("--headless")
options.add_experimental_option("excludeSwitches", ["enable-automation"])
options.add_experimental_option('useAutomationExtension', False)
```

### Fetch content from a website
As I mentioned earlier,  `requests.get()` doesn't work on the website, so I decided to use a Selenium driver instead. I also used Selenium Stealth to avoid detection by CloudFlare. To scrape data from all pages, I implemented a `while` loop iterating through the page numbers.

```
try:
  while True:
    try:
    url = f'https://www.ddproperty.com/en/property-for-rent/{page_num}?freetext=Bangkok&property_type=N&property_type_code%5B0%5D=CONDO&region_code=TH10&search=true'
    driver = webdriver.Chrome(options=options)
    stealth(driver,
            languages=["en-US", "en"],
            vendor="Google Inc.",
            platform="Win32",
            webgl_vendor="Intel Inc.",
            renderer="Intel Iris OpenGL Engine",
            fix_hairline=True,
        )
    
    driver.get(url)
    soup = BeautifulSoup(driver.page_source, 'html.parser')
```

  

### Find element
Example of finding element in soup

```
soup.findAll('div', class_='row')
```

Then decided to use `.text` or `.text.strip()` directly to extract the desired data. Additionally, I set a condition to break the loop if a specific element is found.

```
if soup.find('li', class_="pagination-next disabled"):
	print("this is the last page")
	break
```

And finally I converted data to the pandas DataFrame using `df = pd.DataFrame()`. Then exported as .csv file.

```
df.to_csv(r'save\to\my\path\file.csv', index=False, encoding='utf-8-sig')
```

Please check my full code [HERE](https://github.com/Imppel-9704/condo-data-web-scraping-project/blob/master/web-scraping.ipynb)

## Data Preparation
Before going to the next step, I follow these data preparation processes:
- Add necessary columns.
- Remove unwanted columns.
- Handle missing data.
- Change data types.

### Python Libraries
```
import pandas as pd 
import numpy as np 
import re
```

Please check my full code [HERE](https://github.com/Imppel-9704/condo-data-web-scraping-project/blob/master/condo_price_data_preparation.ipynb)

## Analysis
### EDA (Exploratory Data Analysis)

For doing EDA I follow these 3 easy steps:
  1. Reading and understanding the dataset
  2. Data Cleaning
  3. Visualization

### Objective
Since the COVID-19 pandemic ended, rental condo prices have seen a slightly increase. As someone who is currently looking for a condo to rent in Bangkok, I'm questioning this:

-   Where are the best areas to live?
-   Which district has the highest rental prices?
-   What factors affect rental prices in different areas?

I believe this information will help me make a decision about where to rent. To answer these questions, I've decided to use Exploratory Data Analysis (EDA) on the data I scraped from the website. 
Please check out my project [HERE](https://github.com/Imppel-9704/condo-data-web-scraping-project/blob/master/condo_price_data_analysis.ipynb)

### Summary
The key findings from using Exploratory Data Analysis (EDA) :
- Distribution of condos: Most condos are located in
	1. Watthana
	2. Khlong Toei
	3. Pathumwan

- Many condos are located near BTS stations.
- The average condo age is 4 years old, with the oldest is 32 years old.
- Rental prices in Pathumwan have the widest range of prices.
- Factors affecting rental price:
- Room size has a strong positive correlation with rental price (R = 0.83).
- The amount of bedrooms also has a strong positive correlation with rental price (R = 0.64).
- The distance from a station to a condo has a weak correlation with rental price (R = 0.16).
