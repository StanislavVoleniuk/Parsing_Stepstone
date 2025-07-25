# Parsing_Stepstone
Parsing Stepstone.de  "Data Engineer"

## Description  
This script collects job listings for "Data Engineer" from the Stepstone.de website. Selenium and BeautifulSoup are used for scraping and data extraction.

## How it works  
1. Launch Chrome browser.  
2. Open Stepstone.de.  
3. Accept cookies.  
4. Enter the query "Data Engineer".  
5. Click search.  
6. Collect data from 5 pages.  
7. Extract company name and job title.  
8. Save data to a pandas DataFrame.

## Script code  
```python
from selenium.webdriver import Chrome
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.by import By
from bs4 import BeautifulSoup
from time import sleep
import pandas as pd

browser = Chrome()
browser.maximize_window()

url = "https://www.stepstone.de/de"
browser.get(url)

try:
    cookie_btn = WebDriverWait(browser, 10).until(
        EC.element_to_be_clickable((By.ID, "ccmgt_explicit_accept"))
    )
    cookie_btn.click()
except:
    print("Cookie баннер не показан")

input_tab = WebDriverWait(browser, 15).until(
    EC.presence_of_element_located((By.CSS_SELECTOR, 'input[data-at="searchbar-keyword-input"]'))
)
input_tab.send_keys("Data Engineer")
sleep(2)

button = WebDriverWait(browser, 15).until(
    EC.element_to_be_clickable((By.CSS_SELECTOR, 'button[data-at="searchbar-search-button"]'))
)
button.click()

data = []
for page in range(5):
    sleep(3)
    soup = BeautifulSoup(browser.page_source, "lxml")
    vacancies = soup.find_all('div', class_='res-urswt')
    for vac in vacancies:
        try:
            company = vac.find('div', class_='res-1r68twq').text.strip()
            job_title = vac.find('a', attrs={"data-at": "job-item-title"}).text.strip()
            data.append([company, job_title])
        except:
            continue
    try:
        next_btn = WebDriverWait(browser, 10).until(
            EC.element_to_be_clickable((By.CLASS_NAME, 'res-1gjjx0j'))
        )
        next_btn.click()
    except:
        print("Конец списка вакансий.")
        break

browser.quit()

df = pd.DataFrame(data, columns=["Компания", "Должность"])
print(df.head())

## Results  
The script collects about 125 job listings (25 per page × 5 pages).  
Data is displayed in the console and saved in a pandas DataFrame.

### Screenshots    
![1](https://github.com/user-attachments/assets/9980bd24-d337-4e99-a30a-fefc896b1bc1) -Terminal output1  

![2](https://github.com/user-attachments/assets/2c7d9d60-e217-4209-bc42-fe8e5398ba4b)  -Terminal output2


