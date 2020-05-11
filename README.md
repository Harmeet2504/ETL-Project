# ETL-Project

# Statement of project: Create a database for NJ demographic and crime data by county in preparation for crime analysis.

# Questions to Answer
What demographic variables (if any) have the largest impact on crime?
We downloaded crime and demographic data for NJ at the county level for regression analysis.
We chose demographics often associated with higher crime rates: unemployment, % below poverty level, unemployment, income level, births to single mothers, sex ratio, age. These will be compared with the 10 offenses reported in the FBI database. With this data in hand, data scientists will be able to reject or accept the null hypothesis

# Requirements
 PostgreSQL, Jupyter Notebook, Sqllite, MongoDB Compass, pgAdmin4, Pandas, pyMongo, psycopg2

# Methodology: The following methodology was adopted:

# 1.Extraction:
# NJ county demographic data (2017):
Extracted from the US Census 2017 Profile data: https://api.census.gov/data/2017/acs/acs5/profile?get=
An API key can be obtained from https://api.census.gov/data/key_signup.html
API data returned as list of lists and converted to pandas dataframe. Please see jupyter notebook in the “codes” folder for the python code. 

# NJ county crime data (2017):
Extracted from the FBI’s Crime Data Explorer API: https://crime-data-explorer.fr.cloud.gov/api
An API key can be obtained from https://api.data.gov/signup/
Data collected from contributing Agencies (town, state and county police departments) which each have their own “ori” code. Data collected for two different tables. One for Agencies: their name, ori code, and county locations. The second for crimes recorded by each agency. The ori code keys link the two tables. 
Data is returned as nested dictionaries which needs some cleaning up before converting into a dataframe. The function “de_nest” was built to convert the Agency API response to a dataframe.
The crime API data is paginated which required nested for loops to extract data from each specific “offense” page. The function “unpack” was built to convert the Crime API response to a dataframe.  Please see jupyter notebook in the “codes” folder for the python code.
NJ.gov: County and zip codes of NJ were scrapped using beautiful soup.

# 2.Transformation: 
# Crime data: 
The agencies data and FBI crime data as mentioned above were merged using ORI key. Unwanted columns were dropped. Columns were renamed and reorganized. Column with county names were converted to title. The transformed data were saved as tables for subsequent loading.

# Demography data: 
This dataset included population, Birth to Unmarried Women, High School Graduation percentage, Unemployment, Below poverty level, Mean Income, Sex-Ratio per hundred women and Median age of people of counties’ in New Jersey. 

Data was filtered by .iloc[] function. Then dataset was converted into DataFrame. Columns were renamed, typos corrected, unwanted columns were dropped, headers were rearranged. The transformed data were saved as tables for subsequent loading.
Jupyter notebook with codes for transformation can be referred in the “codes” folder. Output tables and csv files can be referred in the “output_files” folder.

# 3.Loading: 
Database was created both on PostgreSQL and MongoDB cluster.
Postgres: Connection was established with PostgreSQL and the data were uploaded.Please see jupyter notebook in the “codes” folder for the python code.

Relations between the tables is shown in the ERD below:
![ERD](https://github.com/Harmeet2504/ETL-Project/blob/master/Entity%20Relationship%20Diagram%20Example%20(UML%20Notation).png)

MongoDB:We also tried uploading data on non-relational “NoSQL” database, MongoDB. To load data, MongoDB cluster was set up on AWS. A user was created and IP address was set up. MongoDB Compass was installed to download, visualize and manipulate data from database. MongoClient was used to communicate with MongoDB using pymongo. Collections were loaded as dictionaries as NoSQL database like MongoDB provides support for JSON-styled, document-oriented storage systems. The database is available in MongoDB. The collections are “demography” and “crime”.
Please see jupyter notebook in the “Load-mongodb” folder for the python code.Showing below screnshot of one of the collections in MongoDB cluster.
![crime_Collection_in_MongoDB_cluster](https://github.com/Harmeet2504/ETL-Project/blob/master/Load-mongodb/crime-collection.png)

# Challenges:
1. Un-packing FBI API responses: FBI's API data came back as a triple-nested dictionary. Other queries came back as lists of nested dictionaries. This was difficult to un-pack as the traditional pd.DataFrame() function returned just one row with jumbled column headers. We built functions to un-pack each type of API response.
2. Looping through FBI's API pagination: When pulling the offense data from FBI's API we found that the API results were paginated. We were already running a for loop to query each type of offense. Now we had to create a nested for loop to read through each page of each API offense response. To get the correct number of pages to loop through, we read the pagination[page] result for each offense
3. One to Many join commands. After loading the data to postgres, we had difficulty creating queries with joining on the shared key "county". This required a complicated GROUP BY, aggregation and LEFT JOIN efforts. Even then we were having trouble getting the results we wanted
 
4. Loading and reading data to and from MongoDB cluster could be performed successfully and queried.  However, querying data from multiple collections could not be performed.  Given the timeframe, we could not resolve this.


