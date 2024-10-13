# About
Python notebook in colab to pdf converter.

# Usage
  - Copy code in below into last cell of colab.
  - Copy url of colab into variable COLAB_URL and run.

# Code
```
GITHUB_GIST_DOMAIN = "https://gist.github.com"
# edit this
COLAB_URL = ""

def get_notebook_param():
  import re
  matched = re.match("https://colab.research.google.com/gist/(.*?)/(.*?)/(.*?)#",COLAB_URL)
  return matched.groups()

def get_archive_id_and_url(github_username, github_gist_id):
  from bs4 import BeautifulSoup as BS
  from urllib.request import Request, urlopen
  import re

  url = '{}/{}/{}'.format(GITHUB_GIST_DOMAIN,github_username, github_gist_id)
  req = Request(url)
  html_page = urlopen(req).read()

  soup = BS(html_page, 'html.parser')
  relative_download_url = soup.findAll('a', string="Download ZIP")[0].get("href")
  archive_id = re.match("/.*?/.*?/archive/(.*).zip",relative_download_url).groups()[0]
  return (archive_id,GITHUB_GIST_DOMAIN+relative_download_url)
  

def download_from_gist(url):
  import urllib.request
  import zipfile

  ! rm -rf /gistColab2Pdf
  ! mkdir /gistColab2Pdf
  urllib.request.urlretrieve(url, "/gistColab2Pdf/0.zip")

  with zipfile.ZipFile("/gistColab2Pdf/0.zip", 'r') as zip_ref:
    zip_ref.extractall("/gistColab2Pdf/unzipped")

def remove_last_cell(file):
  import json
  with open(file) as f:
    d = json.load(f)
    d["cells"]=d["cells"][:-1]
    with open(file,"w") as f:
      json.dump(d, f)

def get_unzipped_folder(github_gist_id, archive_id):
  return "/gistColab2Pdf/unzipped/{}-{}".format(github_gist_id, archive_id)

def convert_to_pdf(unzipped_folder, github_title):
  ! pip install --quiet -U nbconvert "PyPDF2<3.0.0" pyppeteer
  ! pip install  --quiet --no-deps notebook-as-pdf
  ! pyppeteer-install
  notebook_location = "{}/{}".format(unzipped_folder,github_title)
  ! jupyter nbconvert --to pdfviahtml $notebook_location
  
def download_to_user(unzipped_folder,github_title):
  from google.colab import files
  pdf_file = "".join(github_title.split(".ipynb")[:-1] + [".pdf"])
  files.download("{}/{}".format(unzipped_folder,pdf_file))

(github_username, github_gist_id, github_title) = get_notebook_param()
(archive_id,download_url) = get_archive_id_and_url(github_username, github_gist_id)
download_from_gist(download_url)
unzipped_folder = get_unzipped_folder(github_gist_id, archive_id)
remove_last_cell("{}/{}".format(unzipped_folder,github_title))
convert_to_pdf(unzipped_folder, github_title)
download_to_user(unzipped_folder,github_title)
```
