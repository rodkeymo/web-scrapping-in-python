# web-scrapping-in-python

in this project we perform a simple dictionary web scrapping in python

Install the Essential Python Libraries

      pip3 install requests beautifulsoup4 
Import the "requests" library to fetch the page content and bs4 (Beautiful Soup) for parsing the HTML page content.

        from bs4 import BeautifulSoup
        import requests
        
        
 we will make a GET request to the url and will create a parse Tree object(soup) with the help of BeautifulSoup and Python built-in "lxml" parser.
 
       # importing the libraries
      from bs4 import BeautifulSoup
      import requests
      import cvs
send HTTP request to the url
      url = "https://en.wikipedia.org/wiki/List_of_countries_by_GDP_(nominal)"

Make a GET request to fetch the raw HTML content
          
           html_content = requests.get(url).text

Parse the html content
 
      soup = BeautifulSoup(html_content, "lxml")
      print(soup.prettify()) # print the parsed data of html
 
Analyze the HTML tag, where your content lives
Create a data dictionary to store the data

      data = {}

Get the table having the class wikitable
      
      gdp_table = soup.find("table", attrs={"class": "wikitable"})
      gdp_table_data = gdp_table.tbody.find_all("tr")  # contains 2 rows
      
Get all the headings of Lists
      
        headings = []
for td in gdp_table_data[0].find_all("td"):
    # remove any newlines and extra spaces from left and right
    headings.append(td.b.text.replace('\n', ' ').strip())

# Get all the 3 tables contained in "gdp_table"

    for table, heading in zip(gdp_table_data[1].find_all("table"), headings):
Get headers of table i.e., Rank, Country, GDP.
     
     t_headers = []
    for th in table.find_all("th"):
remove any newlines and extra spaces from left and right

      t_headers.append(th.text.replace('\n', ' ').strip())
Get all the rows of table
    
    table_data = []
    for tr in table.tbody.find_all("tr"): # find all tr's from table's tbody
        t_row = {}
  
Each table row is stored in the form of
        
        t_row = {'Rank': '', 'Country/Territory': '', 'GDP(US$million)': ''}

find all td's(3) in tr and zip it with t_header
       
       for td, th in zip(tr.find_all("td"), t_headers): 
            t_row[th] = td.text.replace('\n', '').strip()
        table_data.append(t_row)

Put the data for the table with his heading.
    
    data[heading] = table_data
    
 EXPORT DATA TO CVS FILE
  """
For this example let's create 3 seperate csv for 
3 tables respectively
"""
      
      for topic, table in data.items():
Create csv file for each table
    
    with open(f"{topic}.csv", 'w') as out_file:
  Each 3 table has headers as following
  
        headers = [ 
            "Country/Territory",
            "GDP(US$million)",
            "Rank"
        ] # == t_headers
        writer = csv.DictWriter(out_file, headers)
        # write the header
        writer.writeheader()
        for row in table:
            if row:
                writer.writerow(row)
                
                
                
