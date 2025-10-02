# AWS-SNOWFLAKE-TABLEAU

## AWS

**Step 1: Creating & Loading Data to S3 Bucket**

First of all I opened my account to create the bucket, and for that I had to choose a storage and as for that I choose S3. After this by clicking on the options create bucket I filled out the nessecary details like, bucket type, region (which is same as in snowflake), & bucket name and like this I created a bucket under tha name, ' '. 

Now that I had created the bucket, I loaded my dataset into the bucket by clicking on the same available in general purpose bucket, after this by simply clicking on upload and then at add files I loaded the dataset into the AWS

**Step 2: Creating a Role**

well the role here is needed to establish a connection between AWS and Snowflake.
and to create a role first of all we need to go on the console home. then under the all services I choose IAM, then under the Access Management by clicking on roles I then clicked on create role then under that, by filling out the 3 step details I created the Role.

## Snowflake + AWS

**Step 1: Creating The Integration Object & Updating The Trust Policy**

For this I first of all clicked on home tab and then under the projects I opened a new worksheet and wrote a piece of code. After this since I needed to update the bucket details so going back to the AWS accounts console home in the all services I choose S3 under Storage, and then I clicked on the bucket I had created previously, and updated the Storage Allowed Location as per the name of the project, and also updated the Storage ARN Role ARN as per the ARN in Role. After that I exceuted the piece of code for Integration Object in snowflake by pressing Ctrl + Enter. Later this I also exceuted the code for updating The trust policy in amazon account in snowflake by selecting it and pressing Ctrl + Enter.

After all that finally I updated the Trust Policy accordingly in AWS. Going to the roles in the Trust Relationships, I clicked on the edit trust policy. Here I updated the "AWS" value to the User ARN value which I got after executing the code in snowflake, apart that I also updated the "sts:externalid" value which i had previously filled with some random values to the values I got after executing the code in snowflake. then I clicked on update policy and updated the policy.

**Step 2: Loading the Data into Snowflake**

Here first of all I created a database



# Dashboard

**Structure and Order**
1. Project Title/ Headline
2. Short Description/ Purpose
3. Tech Stack
4. Data Source
5. Features/Highlights
6. Screenshots

**1. Project Title/ Headline**
A concise, descriptive name for the dashboard.
Example:
Product-Sales: Product Sales Insights Dashboard
A dynamic, interactive data visualization tool built to explore product sales data of different countries, trends by years, profits, and sales in different segments.

**2. Short Description/ Purpose**
Example:
Energy Consumption Dashboard is a visually engaging and analytical Tableau report designed to help users explore and compare KWH and CSU by country, region, and energy source, across multiple countries. The dashboard focuses on highlighting Top 6 KWH by region, Top 6 CSU by region, Top 5 KWH Energy Source, Top 5 CSU Energy Source, KWH by country and CSU by country. With trends in the year 2013 and 2014. Showcasing total sales and its percentage in segments like Government, Midmarket, Enterprise, Channel Partners, and Small Businesses.

**3. Tech Stack**
Example:
AWS+Snowflake - 
Tableau - Main data visualization platform used for report creation.
File Format - .twb for development and .png for dashboard previous.

**4. Data Source**
Example:
Source: https://www.udemy.com/course/complete-data-analyst-bootcamp-from-basics-to-advanced/learn/lecture/47789127#overview

**5. Features/Highlights**
1. Key Questions
2. Goal of the Dashboard
3. A Walkthrough of Key Visuals

Example:

**1. Key Questions:**
Which Country/Region generates more profit? 
Which Country/Region has more sales? 
What's the average sales of Products? 
What's the total quantity sold in different countries?

**2. Goal of the Dashboard:**
To deliver an interactive visual tool that enables the users to explore the data with ease. Uncover the sales trend.

**3. A Walkthrough of Key Visuals:**

#Multi-Row Card (top): Shows a total of 118,726,350.26 in sales revenue, yielding a substantial total profit of 16,893,702.26. With gross sales amounting to 127,931,598.50, the total quantity sold reached an impressive 1,125,806 units. 

#Total Quantity Sold by Segments (Pie Chart):
The pie chart provides a breakdown of the quantities sold across distinct market segments, including Government, Midmarket, Enterprise, Channel Partners, and Small Businesses. This segmentation highlights the sales distribution and emphasizes the performance in each category.

#Total Discounts by Discount Bands (Funnel):
The funnel visualization illustrates the various discount tiers categorized as High, Medium, and Low. Each tier is accompanied by its corresponding discount values, allowing for a clear understanding of the pricing strategy employed across different offers.

#Trend of Sales In Different Countries by Year (Waterfall Chart):
The Waterfall Chart vividly illustrates the sales performance for the years 2013 and 2014, showcasing a dynamic visual flow of sales growth across various countries. It captures not only the total increase in sales over this period but also highlights the changes in sales figures between the two years, offering a clear perspective on trends and changes in each market.

#Top 5 Products by Profit (Area Chart):
The area chart displays the top five products ranked by their profit contributions, illustrating a clear comparison of their performance. 
1. **Paseo** stands out as the leading product, generating an impressive profit of **$4.8 million**. 
2. Following  **VTT**, which accrued a profit of **$3.0 million**, showcasing its strong market presence.
3. In third place, **Amarilla** achieved a profit of **$2.8 million**, reflecting its popularity among consumers.
4. **Velo** comes in fourth with a profit of **$2.3 million**, indicating a solid demand for this product. 
5. Lastly, **Montana** with a profit of **$2.1 million**, highlights its reliability and appeal.

#Top 5 Product By Quantity (Area Chart): The Area Chart illustrates the top five products based on sales quantity, showcasing their impressive performance within the market. 
The leading product, **Paseo** stands out with a significant total of 0.34 million units sold. Following closely is **VTT** , with sales reaching 0.17 million units, indicating strong demand. **Velo** and **Amarilla** both share a notable sales figure of 0.16 million units each, reflecting their comparable market presence and appeal to a similar customer base. Lastly, Montana rounds out the list with a respectable 0.15 million units sold, contributing to its overall success.


#Top 5 Product By Quantity (Area Chart):
The area chart illustrates the top five products ranked by quantity sold, providing a clear visual representation of their performance. 
1. **Paseo** leads the chart with an impressive 33 million units sold, highlighting its popularity and strong demand in the market.
2. **VTT** follows in second place with sales reaching 21 million units, indicating solid consumer interest and a robust market presence.
3. In third position, **Velo** achieves a notable 18 million units sold, demonstrating its appeal among customers and contributing significantly to overall sales.
4. Also tied with 18 million units, **Amarilla** captures the fourth spot, showcasing that it resonates well with buyers and is on par with Velo in terms of popularity.
5. Finally, **Montana** rounds out the top five with 15 million units sold, reinforcing its status as a competitive product within the lineup.

#Manufacturing Cost Of Different Products In Different Countries (Clustered Column Chart):
The Clustered Column Chart visually represents the manufacturing costs associated with various products across several countries, specifically Canada, France, Germany, Mexico, and the United States of America.

#Profit and Sales by Year, Quarter, Month and Day (Line Chart):
The line chart presents a detailed comparison of profit and sales figures for the years 2013 and 2014. It breaks down the data into various segments, including quarterly, monthly, and daily analyses. 

**6.Screenshots:**
See what the dashboard looks like - ![Alt Text](https://github.com/Devi27-create/Product-Sales-Dashboard/blob/main/Total%20Quantity%20Sold%20By%20Segment%20Trends%20Of%20Sales%20In%20Different%20Countries%20Total%20Discounts%20by%20Discount%20Bands.png](https://github.com/Devi27-create/AWS-SNOWFLAKE-TABLEAU/blob/main/Energy_Consumption_dashboard.png).



