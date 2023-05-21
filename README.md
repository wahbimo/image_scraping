# Image Scraping
Downloading a list of images from the web using selenium library in Python and duckduckgo.com.

# Script: Image Scraping using duckduckgo.com

***
***
***

***Selenium*** is a tool to automate the testing of web apps.


```python
!pip install selenium
```

## Importation of libraries:

***os :*** to use operating system interfaces
***urllib :*** to manipulate URLs
***pandas :*** to manipulate Data Frames


```python
import os

import selenium.webdriver as webdriver
from selenium.webdriver.firefox.service import Service
from selenium.webdriver.firefox.options import Options
from selenium.webdriver import ActionChains

import urllib.request

import pandas as pd
```

### Loading the descriptions (/names/titles...) of the images we want to download


```python
df = pd.read_excel("yourExcelFile.xlsx","sheetName")
image_description_column = df["Description"]
```

### Defining the function to download an image from a link

We store _file_name_ in _file_path_ as _jpg_ image


```python
def download_image(url, file_path, file_name):
    full_path = file_path + file_name + '.jpg'
    urllib.request.urlretrieve(url, full_path)
```

***
***
***

I am using Firefox for this demo.
You have to download the geckodriver for firefox from this link:
[Github for geckodriver](https://github.com/mozilla/geckodriver/releases)
I placed _'geckodriver.exe'_ in a directory I created _'Drivers'_ located in the same mother directory as this notebook.


```python
user_agent = 'Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:109.0) Gecko/20100101 Firefox/111.0'
FireFoxDriverPath = os.path.join(os.getcwd(), 'Drivers', 'geckodriver.exe')
firefox_service = Service(FireFoxDriverPath)
firefox_option = Options()
firefox_option.set_preference("general.useragent.override", user_agent)
browser = webdriver.Firefox(service=firefox_service, options=firefox_option)
browser.implicitly_wait(7)

browser.maximize_window() #Maximise the window not to have errors when using xPaths
action = ActionChains(browser) #to use the mouse clicks
```

### Loop over the descriptions of the images


```python
i = 0
for name in image_description_column:

    url = "https://duckduckgo.com/?q="+(name.strip()).replace(" ","+")+"&va=b&t=hc&iar=images&iax=images&ia=images"
    browser.get(url)

    image_box = browser.find_element('xpath','/html/body/div[2]/div[3]/div/div/div[2]/div[2]/div[1]/div[1]/span/img')
    #use Inspect to get the right xPath address of the first result of the search,it must look like this one '/html/body/div[2]/div[3]/div/div/div[2]/div[2]/div[1]/div[1]/span/img'
    action.click(image_box).perform()#Click on the first image

    img = browser.find_element('xpath','/html/body/div[2]/div[3]/div/div[2]/div/div[1]/div[1]/div/div[1]/div/a/img[1]')
    #When clicking on the first result a bigger image appears, it is img, so make sure to correct the xPath.

    src = img.get_attribute('src')#getting the link of the image
    file_path = r"C:\Users\...Your path" #put your file path here
    file_name = "image"+str(i)  #the name of the image is: image0.jpg, image1.jpg....
    download_image(src, file_path, file_name)
    print("image __"+str(i)+"__ downloaded successfully.")
    i += 1
```

***
***
***
