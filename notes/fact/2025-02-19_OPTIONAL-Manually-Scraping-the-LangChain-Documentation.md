---
date: 2025-02-19
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# OPTIONAL Manually Scraping the LangChain Documentation

Use the following command to scrape the LangChain documentation:

```sh
!wget -r -A.html -P langchain-docs https://langchain.readthedocs.io/en/latest/

# -r: recursive download, so it'll download the child pages as well
# -A.html: -A is for "Accept", so it'll only download .html files
# -P langchain-docs: -P is for "Prefix", so it'll save the files in the langchain-docs directory
# https://langchain.readthedocs.io/en/latest/: the URL of the website to download
```

If not working, maybe try to change url:

```sh
https://api.python.langchain.com/en/latest/langchain_api_reference.html
```

If still not working, try to use the following commands:
```sh
wget -r -A.html -P langchain-docs https://api.python.langchain.com/en/v0.1/api_reference.html
!wget -r -A.html -P langchain-docs https://api.python.langchain.com/en/v0.1/api_reference.html
wget -r --no-parent --html-extension -P langchain-docs https://api.python.langchain.com/en/v0.1/api_reference.html
wget -r -A.html -P langchain-docs https://api.python.langchain.com/en/v0.1/api_reference.html
```

If still not working, try to use the following python script with the URL.

```py
import requests
from bs4 import BeautifulSoup
import os
import urllib
 
# The URL to scrape
url = "https://api.python.langchain.com/en/latest/api_reference.html"
 
# The directory to store files in
output_dir = "./langchain-docs/"
 
# Create the output directory if it doesn't exist
os.makedirs(output_dir, exist_ok=True)
 
# Fetch the page
response = requests.get(url)
soup = BeautifulSoup(response.text, 'html.parser')
 
# Find all links to .html files
links = soup.find_all('a', href=True)
 
for link in links:
    href = link['href']
    
    # If it's a .html file
    if href.endswith('.html'):
        # Make a full URL if necessary
        if not href.startswith('http'):
            href = urllib.parse.urljoin(url, href)
            
        # Fetch the .html file
        file_response = requests.get(href)
        
        # Write it to a file
        file_name = os.path.join(output_dir, os.path.basename(href))
        with open(file_name, 'w', encoding='utf-8') as file:
            file.write(file_response.text)

```

Please note that the entire process may take several hours, but the LangChain documentation is constantly updated. Therefore, the course provides direct download of the zip file, which will also be frequently updated.




