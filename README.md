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

Finally, I queried the updated table once again to review the changes:

```sql
SELECT * FROM energy_consumption;
```


## Dashboard

After successfully linking Snowflake and Tableau using the specified server details, I embarked on the process of creating the dashboard. This stage involved the following structure and order to ensure clarity and effectiveness in data presentation.

**Structure and Order**
1. Dashboard Title/ Headline
2. Short Description
3. Tech Stack
4. Data Source
5. Features/Highlights
6. Screenshots

**1. Project Title/ Headline:**

***Dashboard Title: Energy Consumption Dashboard***  
This project presents an innovative and interactive data visualization tool designed to facilitate the exploration of KWH and CSU across various countries.

**2. Short Description:**

Energy Consumption Dashboard is a visually engaging and analytical Tableau report designed to help users explore and compare KWH and CSU by country, region, and energy source, across multiple countries. The dashboard focuses on highlighting Top 6 KWH by region, Top 6 CSU by region, Top 5 KWH Energy Source, Top 5 CSU Energy Source, KWH by country and CSU by country.
The Energy Consumption Dashboard serves as an engaging and analytical Tableau report, desiged to empower users in examining and comparing energy usage metrics, specifically KWH and CSU, by country, region, and energy source. The dashboard is structured to emphasize key insights, including the Top 6 KWH and Top 6 CSU by region, as well as the Top 5 KWH and Top 5 CSU by energy source. Users can seamlessly navigate the data to view KWH and CSU metrics on a country-by-country basis, enabling a comprehensive understanding of energy consumption patterns across the globe. This tool is particularly valuable for policymakers, researchers, and businesses seeking to analyze energy efficiency and sustainability initiatives.

**3. Tech Stack:**

-**AWS+Snowflake-** Utilized for robust data storage and management, supporting large-scale data processing essential for real-time analysis
-**Tableau-** The primary data visualization platform used to create the dashboard, offering advanced graphical representations and interactive features that enhance user engagement and comprehension of the data.
-**File Format-** The dashboard is developed in .twb format for easy updates and maintenance, with snapshot visualizations saved as .png files for sharing and reporting purposes. 

**4. Data Source:**
Source: https://www.udemy.com/course/complete-data-analyst-bootcamp-from-basics-to-advanced/learn/lecture/47789127#overview

**5. Features/Highlights:**

1. Key Questions
2. Goal of the Dashboard
3. A Walkthrough of Key Visuals


**1. Key Questions:**

- **Energy Source Contribution:** Which specific energy sources are responsible for generating the highest amounts of KWH and CSU? (Understanding the contribution of various sources, such as solar, wind, fossil fuels, and hydroelectric power, is crucial for evaluating efficiency and sustainability.)

- **International Usage Patterns:** Which countries show the highest levels of KWH and CSU consumption? (Identifying these countries allows for a comparative analysis of energy usage trends and highlights potential areas for energy conservation efforts.)

- **Regional Analysis:** What are the top six regions based on KWH and CSU usage? (This insight will aid in recognizing geographical patterns in energy consumption and guide resource allocation for energy initiatives.)

**2. Goal of the Dashboard:**

The primary objective of this dashboard is to provide users with an interactive and intuitive visual tool that simplifies data exploration and analysis. By allowing users to seamlessly navigate through the dataset. The dashboard helps to uncover intricate details regarding the usage patterns of KWH and CSU. This ultimately facilitates informed decision-making and encourages strategic planning for energy management and sustainability efforts.

**3. A Walkthrough of Key Visuals:**

**1. Text Box (top):**
The text box displays the title of the Dashboard, ***Energy Consumption Dashboard***.

**2. KWH by Country (Bar Chart) (Left Corner):**
This visually engaging bar chart illustrates the electric consumption in kilowatt-hours (KWH) across various countries. Australia leads the pack with an impressive usage of 80,410 KWH, highlighting its high energy demands. In contrast, Chile registers the lowest energy consumption among the listed countries, with a modest usage of 16,556 KWH, reflecting its comparatively lower energy requirements.

**3. CSU by Country (Bar Chart) (Middle):**
The bar chart presented here captures the energy usage in CSU by country. New Zealand stands out as the largest consumer, utilizing 16,862 CSU, indicative of its significant energy needs. Conversely, Nigeria occupies the bottom position with a total of 5,043 CSU, while Kenya follows closely behind at 5,126 CSU, illustrating the varying energy consumption patterns across these nations.

**4. KWH by Region (Column Chart) (Top Right Corner):**
This column chart effectively displays the energy consumption in KWH among the top six regions worldwide. Leading this chart is a region with a staggering total of 163,213 KWH, demonstrating substantial energy requirements. Notably, Australia appears at the lower end of this spectrum, with a usage of 145,825 KWH, indicating its relative energy consumption compared to other regions.

**5. KWH by Energy Source (Column Chart) (Top Second Right Corner):**
In this column chart, the five primary energy sources are analyzed based on their usage in KWH. Wind energy emerges as the most utilized source, with a remarkable consumption of 214,492 KWH, showcasing its growing importance in the energy mix. In contrast, the Geothermal Energy Source represents the smallest share, with a usage of 164,182 KWH, underscoring the need for increased investment in this area.

**6. CSU by Region (Column Chart) (Bottom Second Right Corner):**
This column chart visually represents the consumption in CSU across the top six regions. South America takes the lead with a notable consumption of 34,692 CSU, while Africa closely follows with 34,444 CSU, reflecting a competitive energy landscape. Meanwhile, North America registers the least consumption among these regions, with a total of 30,732 CSU, indicating differing energy usage patterns.

**7. CSU by Energy Source (Column Chart) (Bottom Right Corner):**
Here, the column chart categorizes the top five energy sources based on their usage in CSU. Wind energy again stands out, with a considerable consumption of 47,507 CSU, emphasizing its significance in energy generation. Conversely, the Geothermal Energy Source records the lowest usage in this context, with 32,841 CSU, pointing to potential areas for development in sustainable energy practices.


**6. Screenshots:**
See what the dashboard looks like - ![Alt Text](https://github.com/Devi27-create/AWS-SNOWFLAKE-TABLEAU/blob/main/Energy_Consumption_dashboard.png)



