# pandas-challenge
# Dependencies and Setup
import pandas as pd
import numpy as np
# File to Load (Remember to Change These)
file_to_load = "Resources/purchase_data.csv"

# Read Purchasing File and store into Pandas data frame
purchase_data = pd.read_csv(file_to_load)
purchase_data.head()
Out[38]:
Purchase ID	SN	Age	Gender	Item ID	Item Name	Price
0	0	Lisim78	20	Male	108	Extraction, Quickblade Of Trembling Hands	3.53
1	1	Lisovynya38	40	Male	143	Frenzied Scimitar	1.56
2	2	Ithergue48	24	Male	92	Final Critic	4.88
3	3	Chamassasya86	24	Male	100	Blindscythe	3.27
4	4	Iskosia90	23	Male	131	Fury	1.44
In [39]:
purchase_data.count()
Out[39]:
Purchase ID    780
SN             780
Age            780
Gender         780
Item ID        780
Item Name      780
Price          780
dtype: int64
Player Count
Display the total number of players
In [40]:
#players = len(purchase_data['SN'].unique())
#Total_Players = len(purchase_data["SN"].unique())

#summary_df = pd.DataFrame()
#summary_df["Total_Players"] = [Total_Players]
#summary_df
#display/set to variable
players = len(purchase_data['SN'].unique())

#set in DF
players_df = pd.DataFrame({"Total # of Players": [len(purchase_data.SN.unique())]})
players_df
Out[40]:
Total # of Players
0	576
Purchasing Analysis (Total)
Run basic calculations to obtain number of unique items, average price, etc.
Create a summary data frame to hold the results
Optional: give the displayed data cleaner formatting
Display the summary data frame
In [41]:
Unique_items = len(purchase_data["Item ID"].unique())


Average_price = purchase_data["Price"].mean()


total_purchases = len(purchase_data['Purchase ID'])


total_revenue = sum(purchase_data["Price"])



#displaying the summary in the data frame
summary_df = pd.DataFrame({
    'Unique items': Unique_items,
    'Average Price': '${:.2f}'.format(Average_price),
    'Total Purchases': total_purchases,
    'Total Revenue': '${:.2f}'.format(total_revenue)
},index=[0])
summary_df
Out[41]:
Unique items	Average Price	Total Purchases	Total Revenue
0	179	$3.05	780	$2379.77
Gender Demographics
Percentage and Count of Male Players
Percentage and Count of Female Players
Percentage and Count of Other / Non-Disclosed
In [42]:
#find unique SN within df to make sure the same player is not recordered twice
gender = purchase_data.drop_duplicates('SN')

#count each gender
gender_count = gender['Gender'].value_counts()

#get percentage
gender_percent = (gender['Gender'].value_counts()/players)*100

#present data in dataframe
gender_df = pd.DataFrame({
    'Count': gender_count,
    'Percent of Total Players': gender_percent.map('{:.2f}%'.format)
})
gender_df
Out[42]:
Count	Percent of Total Players
Male	484	84.03%
Female	81	14.06%
Other / Non-Disclosed	11	1.91%
Purchasing Analysis (Gender)
Run basic calculations to obtain purchase count, avg. purchase price, avg. purchase total per person etc. by gender
Create a summary data frame to hold the results
Optional: give the displayed data cleaner formatting
Display the summary data frame
In [43]:
#purchases by each gender
gender_purchase_data = purchase_data.groupby('Gender')
gender_purchase_count = gender_purchase_data['Gender'].count()
gender_purchase_count #test works

#average purchase pirce
gender_purchase_mean = gender_purchase_data['Price'].mean()
gender_purchase_mean #test works

#total revenue
gender_purchase_total = gender_purchase_data['Price'].sum()
gender_purchase_total #test works
Out[43]:
Gender
Female                    361.94
Male                     1967.64
Other / Non-Disclosed      50.19
Name: Price, dtype: float64
In [45]:
gender_purchase_data_df = pd.DataFrame({
    'Number of Players': gender_count,
    'Purchase Count': gender_purchase_count,
    'Avg Purchase Price': gender_purchase_mean.map('${:,.2f}'.format),
    'Total Revenue': gender_purchase_total.map('${:,.2f}'.format),
})
gender_purchase_data_df
Out[45]:
Number of Players	Purchase Count	Avg Purchase Price	Total Revenue
Female	81	113	$3.20	$361.94
Male	484	652	$3.02	$1,967.64
Other / Non-Disclosed	11	15	$3.35	$50.19
Age Demographics
Establish bins for ages
Categorize the existing players using the age bins. Hint: use pd.cut()
Calculate the numbers and percentages by age group
Create a summary data frame to hold the results
Optional: round the percentage column to two decimal points
Display Age Demographics Table
In [11]:
#Age demographic bins
age_bins = [0, 18, 24, 35, 50]
age_range = ["kids/teens","young adults","adults", "middle-aged"]
In [18]:
#categorize the existing players using the age bins
purchase_data['age_group'] =  pd.cut(purchase_data['Age'], age_bins, labels=age_range)
purchase_data.head()
Out[18]:
Purchase ID	SN	Age	Gender	Item ID	Item Name	Price	age_group
0	0	Lisim78	20	Male	108	Extraction, Quickblade Of Trembling Hands	3.53	young adults
1	1	Lisovynya38	40	Male	143	Frenzied Scimitar	1.56	middle-aged
2	2	Ithergue48	24	Male	92	Final Critic	4.88	young adults
3	3	Chamassasya86	24	Male	100	Blindscythe	3.27	young adults
4	4	Iskosia90	23	Male	131	Fury	1.44	young adults
In [23]:
#groupby
players_by_age = purchase_data.groupby('age_group').SN.nunique()

#create dataframe to show total count and percentage of total players for each group
age_demo = pd.DataFrame({
    'Total Count': players_by_age,
    'Percentage of Players': (players_by_age / purchase_data.SN.nunique() * 100).map("{:.2f}".format)
})
age_demo
Out[23]:
Total Count	Percentage of Players
age_group		
kids/teens	129	22.40
young adults	275	47.74
adults	139	24.13
middle-aged	33	5.73
Purchasing Analysis (Age)
In [24]:
#isolate columns needed for chunk in two frames
purchase_count_age = purchase_data[['age_group', 'Purchase ID']]
purchase_values_age = purchase_data[['age_group', 'Price']]

#totals purchase by age bin
total_purchase_age = purchase_count_age.groupby('age_group').count() 

#Dollar values of purchase by age
total_purchase_values_age = purchase_values_age.groupby('age_group').sum()

#display summary df
purchasing_age_analysis = pd.DataFrame({
    'Purchase Count': total_purchase_age['Purchase ID'],
    'Average Purchase Price': (total_purchase_values_age['Price'] / total_purchase_age['Purchase ID']).map("${:.2f}".format),
    'Total Purchase Value': total_purchase_values_age['Price'].map("${:.2f}".format)
})
purchasing_age_analysis
Out[24]:
Purchase Count	Average Purchase Price	Total Purchase Value
age_group			
kids/teens	164	$3.07	$502.82
young adults	388	$3.05	$1184.04
adults	188	$2.97	$559.03
middle-aged	40	$3.35	$133.88
Bin the purchase_data data frame by age
Run basic calculations to obtain purchase count, avg. purchase price, avg. purchase total per person etc. in the table below
Create a summary data frame to hold the results
Optional: give the displayed data cleaner formatting
Display the summary data frame
In [ ]:
 
Top Spenders
Run basic calculations to obtain the results in the table below
Create a summary data frame to hold the results
Sort the total purchase value column in descending order
Optional: give the displayed data cleaner formatting
Display a preview of the summary data frame
In [26]:
#Count purchases made by unique SN
spender_purchase = purchase_data["SN"].value_counts()

#Calculate the average purchase by unique SN
spender_average_purchase = purchase_data["Price"].groupby(purchase_data["SN"]).mean()

#Calculate the total revenue from each unique SN
spender_total_purchase = purchase_data["Price"].groupby(purchase_data["SN"]).sum()

#display summary df
big_spenders = pd.DataFrame({
    "Purchase Count":spender_purchase,
    "Average Purchase Price":spender_average_purchase,
    "Total Purchase Value":spender_total_purchase
})

#create df, sort and format
big_spenders_sorted = big_spenders.sort_values("Total Purchase Value", ascending= False)
big_spenders_sorted["Average Purchase Price"] = big_spenders_sorted["Average Purchase Price"].map("${:.2f}".format)
big_spenders_sorted["Total Purchase Value"] = big_spenders_sorted["Total Purchase Value"].map("${:.2f}".format)

#print top 5
big_spenders_sorted[:5]
Out[26]:
Purchase Count	Average Purchase Price	Total Purchase Value
Lisosia93	5	$3.79	$18.96
Idastidru52	4	$3.86	$15.45
Chamjask73	3	$4.61	$13.83
Iral74	4	$3.41	$13.62
Iskadarya95	3	$4.37	$13.10
Most Popular Items
Retrieve the Item ID, Item Name, and Item Price columns
Group by Item ID and Item Name. Perform calculations to obtain purchase count, average item price, and total purchase value
Create a summary data frame to hold the results
Sort the purchase count column in descending order
Optional: give the displayed data cleaner formatting
Display a preview of the summary data frame
In [27]:
#isolate columned needed
popular_items = purchase_data[['Item ID', 'Item Name', 'Price']]


#count transactions for each item
items_purchase = popular_items.groupby(["Item ID","Item Name"])["Price"].count()
#Price of each item
item_price= popular_items.groupby(["Item ID","Item Name"])["Price"].mean()
#Calculate total revenue from each item
popular_purchase_value = popular_items.groupby(["Item ID","Item Name"])["Price"].sum()

#display summary df
popular_items = pd.DataFrame({
    "Purchase Count":items_purchase,
    "Item Price":item_price,
    "Total Purchase Value":popular_purchase_value
})


#create new df, sort and format
popular_items_sorted = popular_items.sort_values("Purchase Count", ascending= False)
popular_items_sorted["Item Price"]= popular_items_sorted["Item Price"].map("${:.2f}".format)
popular_items_sorted["Total Purchase Value"]= popular_items_sorted["Total Purchase Value"].map("${:.2f}".format)

#print the DF
popular_items_sorted[:5]
Out[27]:
Purchase Count	Item Price	Total Purchase Value
Item ID	Item Name			
92	Final Critic	13	$4.61	$59.99
178	Oathbreaker, Last Hope of the Breaking Storm	12	$4.23	$50.76
145	Fiery Glass Crusader	9	$4.58	$41.22
132	Persuasion	9	$3.22	$28.99
108	Extraction, Quickblade Of Trembling Hands	9	$3.53	$31.77
Most Profitable Items
Sort the above table by total purchase value in descending order
Optional: give the displayed data cleaner formatting
Display a preview of the data frame
In [28]:
#re-sort above table by total purchase
popular_items_sorted_purchase = popular_items.sort_values("Total Purchase Value", ascending= False)
popular_items_sorted_purchase["Item Price"]= popular_items_sorted_purchase["Item Price"].map("${:.2f}".format)
popular_items_sorted_purchase["Total Purchase Value"]= popular_items_sorted_purchase["Total Purchase Value"].map("${:.2f}".format)

#print the DF
popular_items_sorted_purchase[:5]
Out[28]:
Purchase Count	Item Price	Total Purchase Value
Item ID	Item Name			
92	Final Critic	13	$4.61	$59.99
178	Oathbreaker, Last Hope of the Breaking Storm	12	$4.23	$50.76
82	Nirvana	9	$4.90	$44.10
145	Fiery Glass Crusader	9	$4.58	$41.22
103	Singed Scalpel	8	$4.35	$34.80
