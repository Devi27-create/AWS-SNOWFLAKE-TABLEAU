# AWS-SNOWFLAKE-TABLEAU(Energy Consumption Dashboard)

## AWS

**Step 1: Creating & Loading Data to S3 Bucket**

To begin the process, I logged into my AWS account to set up a new S3 bucket. This required me to select S3 as my storage option, which is ideal for scalable cloud storage. After clicking on the "Create Bucket" option, I was prompted to fill in several necessary details like, selected the type of bucket, region (ensuring it matched the region used in Snowflake for seamless integration), and bucket name, which i named as 'tableau.project2e.'

Once the bucket 'tableau.project2e' was successfully created, I proceeded to load my dataset into it. The user-friendly interface allowed me to navigate to the general-purpose bucket settings, where I found the "Upload" button. By clicking on this option, I was prompted to "Add Files," which opened a dialog for me to select the dataset I wanted to upload. After selecting the appropriate files from my local storage, I initiated the upload process, and within moments, my dataset was securely stored in the AWS bucket.

**Step 2: Creating a Role**

Well to facilitate a smooth connection between AWS and Snowflake, it was essential to create a role. I started this process by navigating to the AWS console home page. From there, I explored the "All Services" section and selected IAM (Identity and Access Management), a critical component for managing access permissions. Under the Access Management tab, I clicked on "Roles," then chose the "Create Role" option to begin the role creation process.

The platform guided me through a three-step setup. I carefully filled in the required details at each step, specifying permissions and trust relationships that would govern the connection between AWS and Snowflake. After reviewing my inputs, I successfully created the role, thereby establishing a crucial link for data transfer and processing between the two platforms.

## Snowflake + AWS

**Step 1: Creating The Integration Object & Updating The Trust Policy** 

To begin the process, I navigated to the Home tab in my application, where I proceeded to the Projects section and opened a new worksheet. In this worksheet, I crafted a script to create a storage integration in Snowflake. The code I used is as follows:

```sql
CREATE OR REPLACE STORAGE INTEGRATION tableau_Integration
TYPE = EXTERNAL_STAGE
STORAGE_PROVIDER = 'S3'
ENABLED = TRUE
STORAGE_AWS_ROLE_ARN = 'arn:aws:iam::124356268240:role/Tableau.Role1ee'
STORAGE_ALLOWED_LOCATIONS = ('s3://tableau.project2e/')
COMMENT = 'Optional Comment'
```

After executing this code, I realized that I needed to update the bucket details to reflect the project specifications accurately. Therefore, I returned to the AWS Management Console, selecting "All Services" and then clicking on “S3” under the Storage category. There, I located the bucket I had previously created and accessed its settings to modify the Storage Allowed Locations to align with the project's name. Additionally, I updated the Storage AWS Role ARN to match the appropriate ARN for the role I intended to use. 

Once these changes were made, I executed the provided code to create the integration object in Snowflake. To verify that everything was set up correctly, I retrieved the details of the integration object using the following command:

```sql
// Description of Integration Object
DESC INTEGRATION tableau_Integration;
```

After confirming the integration's details, I turned my attention to updating the Trust Policy in AWS. I navigated to the IAM Roles section and selected the role associated with the integration. Under the Trust Relationships tab, I clicked on "Edit Trust Policy." 

Here, I meticulously replaced the "AWS" value with the User ARN that I retrieved earlier from executing the Snowflake code. Moreover, I updated the "sts:externalid" value, which I had initially set using arbitrary values, changing it to the specific values provided by Snowflake after executing the integration code. Once all modifications were completed, I clicked on “Update Policy” to save the changes, thereby finalizing the integration and trust relationship setup.

**Step 2: Loading the Data into Snowflake**

Here first of all I wrote a code to create a database naming "tableau," and executed the code. Following that, I created a schema called "tableau_data," which serves as the organizational structure within the database, and executed the corresponding code. After setting up the schema, I proceeded to create a blank table titled "tableau_dataset." This table was designed with columns and their respective data types, tailored to accommodate the specific dataset. The execution of this code, resulted in a properly structured table. Here’s the code I utilized for creating the database, schema, and table:

```sql
CREATE database tableau;

create schema tableau_Data;

create table tableau_dataset (
Household_ID	string,Region	string,Country	string,Energy_Source	string,
Monthly_Usage_kWh	float,Year	int,Household_Size	int,Income_Level	string,
Urban_Rural	string,Adoption_Year	int,Subsidy_Received	string,Cost_Savings_USD float

);
```

Now I needed to create a stage to bring the data into snowflake and for that I wrote another code mentioning the stage name as tableau.tableau_dataset.tableau_stage then the url and storage_integration and then executed this.
-- Code used:
Next, I needed to create a stage to facilitate the data import into Snowflake and for that I wrote a separate code that defined the stage name as "tableau.tableau_Data.tableau_stage," including the necessary URL and storage integration details. After executing this code, the stage was successfully created.

Here’s the code used for this step:

```sql
create stage tableau.tableau_Data.tableau_stage
url = 's3://tableau.project2e'
storage_integration = tableau_Integration
```

After this, I transferred the data from the created stage "tableau_stage" into the "tableau_dataset" table. For this operation, I executed a copy command and ran a select statement to verify that the data loaded correctly into Snowflake.

Here’s the code utilized for copying data and executing the selection:

```sql
copy into tableau_dataset 
from @tableau_stage
file_format = (type=csv field_delimiter=',' skip_header=1 )
on_error = 'continue'

select * from tableau_dataset;
```
This step effectively ensured that the data was accurately imported, and allowed me to view the entries in the "tableau_dataset" table.

**Step 3: Data Transformations** 
After loading the data and gaining a comprehensive understanding of its structure and content, I proceeded to implement several data transformations. To ensure the original dataset remained intact, I created a new table called "energy_consumption". This was accomplished using the following SQL command:

```sql
CREATE TABLE energy_consumption AS SELECT * FROM tableau_dataset;
```

With the new table in place, I focused on transforming the **Monthly_usage_kwh** column. The objective was to adjust the energy consumption values based on income levels. Specifically, I increased the monthly usage by 10% for low-income levels, 20% for middle-income levels, and 30% for high-income levels. The SQL updates for these transformations were executed as follows:

```sql
-- For low income level
UPDATE energy_consumption
SET monthly_usage_kwh = monthly_usage_kwh * 1.1
WHERE income_level = 'Low';

-- For middle income level
UPDATE energy_consumption
SET monthly_usage_kwh = monthly_usage_kwh * 1.2
WHERE income_level = 'Middle';

-- For high income level
UPDATE energy_consumption
SET monthly_usage_kwh = monthly_usage_kwh * 1.3
WHERE income_level = 'High';
```

After implementing these adjustments, I retrieved the updated records with the command:

```sql
SELECT * FROM energy_consumption;
```

In addition to the modifications made to the monthly usage, I also focused on transforming the **Cost_Savings_USD** column. For this adjustment, I aimed to lower the values across all income levels: reducing by 10% for low-income levels, 20% for middle-income levels, and 30% for high-income levels. The corresponding SQL update statements were as follows:

```sql
-- For low income level
UPDATE energy_consumption
SET cost_savings_usd = cost_savings_usd * 0.9
WHERE income_level = 'Low';

-- For middle income level
UPDATE energy_consumption
SET cost_savings_usd = cost_savings_usd * 0.8
WHERE income_level = 'Middle';

-- For high income level
UPDATE energy_consumption
SET cost_savings_usd = cost_savings_usd * 0.7
WHERE income_level = 'High';
```

Finally,I queried the updated table once again to review the changes:

```sql
SELECT * FROM energy_consumption;
```


## Dashboard

**Structure and Order**
1. Project Title/ Headline
2. Short Description
3. Tech Stack
4. Data Source
5. Features/Highlights
6. Screenshots

**1. Project Title/ Headline**
***Project Title: Energy Consumption Dashboard***  
This project presents an innovative and interactive data visualization tool designed to facilitate the exploration of kilowatt-hour (KWH) and carbon stack usage (CSU) across various countries.

**2. Short Description**
Energy Consumption Dashboard is a visually engaging and analytical Tableau report designed to help users explore and compare KWH and CSU by country, region, and energy source, across multiple countries. The dashboard focuses on highlighting Top 6 KWH by region, Top 6 CSU by region, Top 5 KWH Energy Source, Top 5 CSU Energy Source, KWH by country and CSU by country.
The Energy Consumption Dashboard serves as an engaging and analytical Tableau report, desiged to empower users in examining and comparing energy usage metrics, specifically KWH and CSU, by country, region, and energy source. The dashboard is structured to emphasize key insights, including the Top 6 KWH and Top 6 CSU by region, as well as the Top 5 KWH and Top 5 CSU by energy source. Users can seamlessly navigate the data to view KWH and CSU metrics on a country-by-country basis, enabling a comprehensive understanding of energy consumption patterns across the globe. This tool is particularly valuable for policymakers, researchers, and businesses seeking to analyze energy efficiency and sustainability initiatives.

**3. Tech Stack**
AWS+Snowflake - Utilized for robust data storage and management, supporting large-scale data processing essential for real-time analysis
Tableau - The primary data visualization platform used to create the dashboard, offering advanced graphical representations and interactive features that enhance user engagement and comprehension of the data.
File Format - The dashboard is developed in .twb format for easy updates and maintenance, with snapshot visualizations saved as .png files for sharing and reporting purposes. 

**4. Data Source**
Source: https://www.udemy.com/course/complete-data-analyst-bootcamp-from-basics-to-advanced/learn/lecture/47789127#overview

**5. Features/Highlights**
1. Key Questions
2. Goal of the Dashboard
3. A Walkthrough of Key Visuals


**1. Key Questions:**

- **Energy Source Contribution:** Which specific energy sources are responsible for generating the highest amounts of KWH and CSU? (Understanding the contribution of various sources, such as solar, wind, fossil fuels, and hydroelectric power, is crucial for evaluating efficiency and sustainability.)

- **International Usage Patterns:** Which countries exhibit the highest levels of KWH and CSU consumption? (Identifying these countries allows for a comparative analysis of energy usage trends and highlights potential areas for energy conservation efforts.)

- **Regional Analysis:** What are the top six regions based on KWH and CSU usage? (This insight will aid in recognizing geographical patterns in energy consumption and guide resource allocation for energy initiatives.)

**2. Goal of the Dashboard:**
To deliver an interactive visual tool that enables the users to explore the data with ease. Uncover the usage of KWH and CSU.
The primary objective of this dashboard is to provide users with an interactive and intuitive visual tool that simplifies data exploration and analysis. By allowing users to seamlessly navigate through the dataset. The dashboard helps to uncover intricate details regarding the usage patterns of KWH and CSU. This ultimately facilitates informed decision-making and encourages strategic planning for energy management and sustainability efforts.

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
See what the dashboard looks like - ![Alt Text](https://github.com/Devi27-create/AWS-SNOWFLAKE-TABLEAU/blob/main/Energy_Consumption_dashboard.png)



