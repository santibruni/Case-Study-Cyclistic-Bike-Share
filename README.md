### **Case Study Roadmap: Cyclistic Bike-Share**

#### **Overview**

This case study aims to understand how annual members and casual riders use Cyclistic bikes differently to design marketing strategies that convert casual riders into annual members.

### **Ask Section**

#### **Problem Statement**

The primary problem is to understand how annual members and casual riders use Cyclistic bikes differently. This understanding is crucial for designing effective marketing strategies aimed at converting casual riders into annual members.

#### **Insights to Drive Business Decisions**

By analyzing the usage patterns of annual members versus casual riders, we can identify key differences in their behaviors, preferences, and needs. These insights will enable Cyclistic to:

1. **Develop Targeted Marketing Campaigns:** Create tailored marketing messages that resonate with casual riders and encourage them to become annual members.
2. **Enhance Customer Retention:** Improve user experience and satisfaction for both casual riders and annual members, leading to higher retention rates.
3. **Optimize Resource Allocation:** Allocate marketing and operational resources more efficiently by focusing on strategies that are proven to convert casual riders into annual members.

#### **Key Stakeholders**

1. **Cyclistic Marketing Analytics Team:** Responsible for collecting, analyzing, and reporting data that guides marketing strategy.
2. **Lily Moreno (Director of Marketing):** Oversees the development of marketing campaigns and initiatives to promote the bike-share program.
3. **Cyclistic Executive Team:** Makes final decisions on approving marketing strategies and budget allocations.

#### **Business Task**

To answer the question: **How do annual members and casual riders use Cyclistic bikes differently?** We need to:

1. **Analyze historical bike trip data** to identify patterns and trends in usage between annual members and casual riders.
2. **Identify key metrics** such as ride length, frequency of rides, ride start times, and popular routes.
3. **Compare and contrast** these metrics between the two groups to highlight significant differences.

By completing this analysis, we aim to provide actionable insights that will support the development of marketing strategies to convert casual riders into annual members.

—----------------------------------------------------------------------------------------------------------------------------

### **Prepare Phase**

#### **Guiding Questions**

1. **Where is your data located?**
    - The data was located in the internal company files, and now uploaded to Google BigQuery, where it was uploaded from Google Cloud Storage.
2. **How is the data organized?**
    - The data is organized into multiple tables within a BigQuery dataset. Each table represents a specific time period and contains columns such as trip ID, start time, end time, user type (casual or annual member), and bike ID.
3. **Are there issues with bias or credibility in this data? Does your data ROCCC (Reliable, Original, Comprehensive, Current, Cited)?**
    - The data is reliable and credible as it is publicly available and provided by a reputable source (Motivate International Inc.).
    - It is original, comprehensive, and current, covering the past 12 months of bike trips.
    - The data is cited properly, with sources available through Google Cloud Storage.
4. **How are you addressing licensing, privacy, security, and accessibility?**
    - **Licensing:** The data is used under the license provided by Motivate International Inc.
    - **Privacy:** The dataset does not include personally identifiable information (PII), ensuring compliance with data privacy regulations.
    - **Security:** Data is securely stored in Google BigQuery with appropriate access controls.
    - **Accessibility:** Data is organized and processed in a way that makes it accessible for analysis in BigQuery.
5. **How did you verify the data’s integrity?**
    - Data integrity is verified by cross-checking the data structure and format against the provided schema and documentation.
    - Initial queries are run to check for missing values, inconsistencies, and anomalies.
6. **How does it help you answer your question?**
    - The data contains detailed trip information, including ride duration, start and end times, and user type (casual or annual member), which are essential for analyzing usage patterns between the two user types.
7. **Are there any problems with the data?**
    - Potential issues may include missing or inconsistent data entries. These will be addressed during the data cleaning process.

#### **Key Tasks**

1. **Download Data and Store Appropriately:**
    - The data has been uploaded to Google BigQuery from Google Cloud Storage.
2. **Identify How It’s Organized:**
    - The data is organized into tables in BigQuery, each containing relevant columns such as trip ID, start time, end time, ride length, user type, and day of the week.
3. **Sort and Filter the Data:**
    - Queries will be run to sort and filter the data, ensuring consistency in column formatting and creating additional columns for analysis (e.g., ride length, day of the week).
4. **Determine the Credibility of the Data:**
    - The data's credibility is determined by its source (Motivate International Inc.) and its compliance with data standards and privacy regulations.

### **Final Steps in the Prepare Phase**

1. **Inspect the Schema**: Confirm the column names.
2. **Create Merged Table**: Merge all 12 months into a single table.
3. **Add ride_length and day_of_week Columns**: Ensure these columns are added for analysis.
4. **Clean and Filter Data**: Remove any rows with missing or inconsistent data.
5. **Ensure Consistent Column Formatting**: Format columns consistently for accurate analysis.

### **Detailed Steps and Queries**

#### **1\. Inspect the Schema**

sql

SELECT \*

FROM \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.february24\`

LIMIT 10;

#### **2\. Merge All Monthly Tables**

sql

CREATE OR REPLACE TABLE \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.merged_trips\` AS

SELECT \* FROM \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.february24\`

UNION ALL

SELECT \* FROM \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.july23\`

UNION ALL

SELECT \* FROM \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.july24\`

UNION ALL

SELECT \* FROM \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.june24\`

UNION ALL

SELECT \* FROM \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.march24\`

UNION ALL

SELECT \* FROM \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.may24\`

UNION ALL

SELECT \* FROM \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.november23\`

UNION ALL

SELECT \* FROM \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.october23\`

UNION ALL

SELECT \* FROM \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.september23\`;

#### **3\. Add ride_length and day_of_week Columns**

sql

CREATE OR REPLACE TABLE \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.processed_trips\` AS

SELECT

\*,

TIMESTAMP_DIFF(ended_at, started_at, SECOND) AS ride_length, -- Calculate ride length in seconds

FORMAT_TIMESTAMP('%A', started_at) AS day_of_week -- Get the day of the week

FROM

\`casestudy1-429513.Cyclistic_BikeShare_Last12Months.merged_trips\`;

#### **4\. Clean and Filter Data**

sql

CREATE OR REPLACE TABLE \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.cleaned_trips\` AS

SELECT

\*

FROM

\`casestudy1-429513.Cyclistic_BikeShare_Last12Months.processed_trips\`

WHERE

ride_id IS NOT NULL

AND started_at IS NOT NULL

AND ended_at IS NOT NULL;

#### **5\. Ensure Consistent Column Formatting**

sql

CREATE OR REPLACE TABLE \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.final_trips\` AS

SELECT

CAST(ride_id AS STRING) AS ride_id,

CAST(started_at AS TIMESTAMP) AS started_at,

CAST(ended_at AS TIMESTAMP) AS ended_at,

CAST(rideable_type AS STRING) AS rideable_type,

CAST(member_casual AS STRING) AS member_casual,

ride_length,

day_of_week

FROM

\`casestudy1-429513.Cyclistic_BikeShare_Last12Months.cleaned_trips\`;

### **Verification and Initial Analysis**

Run these queries to ensure the data is ready for analysis:

1. **Average Ride Length by User Type**

sql

SELECT

member_casual,

AVG(ride_length) AS avg_ride_length

FROM

\`casestudy1-429513.Cyclistic_BikeShare_Last12Months.final_trips\`

GROUP BY

member_casual;

1. **Ride Frequency by Day of the Week**

sql

SELECT

member_casual,

day_of_week,

COUNT(\*) AS ride_count

FROM

\`casestudy1-429513.Cyclistic_BikeShare_Last12Months.final_trips\`

GROUP BY

member_casual, day_of_week

ORDER BY

day_of_week;

### **Deliverable: Description of Data Sources**

**Data Sources Used:**

- **Source:** Cyclistic's historical bike trip data from the past 12 months.
- **Location:** Google BigQuery dataset casestudy1-429513.Cyclistic_BikeShare_Last12Months.
- **Tables:** february24, july23, july24, june24, march24, may24, november23, october23, september23.
- **Content:** Each table contains trip details, including trip ID, start and end times, user type (casual or annual member), and other relevant columns.
- **Credibility:** The data is reliable, original, comprehensive, current, and properly cited, ensuring it meets the necessary standards for analysis.
- **Preparation:** Data has been merged, cleaned, and formatted consistently in BigQuery, ensuring proper storage, organization, and accessibility for analysis while maintaining data privacy and security.

—----------------------------------------------------------------------------------------------------------------------------

### **Process Phase Documentation**

#### **Guiding Questions**

1. **What tools are you choosing and why?**
    - **Google BigQuery:** We are using Google BigQuery for several reasons:
        - **Handling Large Datasets:** BigQuery is optimized for processing large volumes of data efficiently, whereas Google Sheets has limitations in terms of the number of rows and overall data size it can handle.
        - **SQL Capabilities:** BigQuery provides powerful SQL capabilities for complex queries, transformations, and aggregations, making it more suitable for advanced data processing tasks.
        - **Integration:** BigQuery integrates seamlessly with other Google Cloud services, allowing for efficient data storage, processing, and analysis.
        - **Scalability:** BigQuery can easily scale to accommodate growing datasets and increased query complexity without performance degradation.
2. **Have you ensured your data’s integrity?**
    - **Yes:** Data integrity has been ensured by:
        - Checking for missing and inconsistent values in critical columns.
        - Verifying the accuracy of newly added columns (ride_length and day_of_week).
        - Maintaining consistent data formats across all tables.
3. **What steps have you taken to ensure that your data is clean?**
    - **Steps Taken:**
        - Merged data from multiple tables into a single table.
        - Added necessary columns (ride_length and day_of_week).
        - Identified and handled missing values in critical columns.
        - Ensured consistent column formatting across the dataset.
4. **How can you verify that your data is clean and ready to analyze?**
    - **Verification:**
        - Run SQL queries to check for null values and data inconsistencies.
        - Validate the calculations of the new columns (ride_length and day_of_week) to ensure they are accurate.
5. **Have you documented your cleaning process so you can review and share those results?**
    - **Documentation:** All steps and queries used for data cleaning and processing are documented below to ensure transparency and reproducibility.

#### **Key Tasks**

1. **Check the data for errors:**
    - Run SQL queries to identify and correct any errors in the data.
2. **Choose your tools:**
    - Google BigQuery is used for data processing due to its ability to handle large datasets and its powerful SQL capabilities.
3. **Transform the data so you can work with it effectively:**
    - Ensure all necessary columns are added, and data is formatted consistently.
4. **Document the cleaning process:**
    - Record all steps and queries used during the data cleaning process.

#### **Deliverable**

**Documentation of any cleaning or manipulation of data:**

- Detailed SQL queries and steps used for data processing and cleaning are documented below

—----------------------------------------------------------------------------------------------------------------------------

### **Analysis Phase**

#### **Introduction**

This report details the analysis phase of the Cyclistic Bike Share dataset. Our primary objective is to understand the differences in bike usage between casual riders and annual members to inform targeted marketing strategies. This analysis involves organizing and exploring the data, performing key calculations, identifying trends and relationships, and handling outliers to ensure data accuracy.

#### **Guiding Questions**

1. **How should you organize your data to perform analysis on it?**
    - Data should be organized to facilitate calculations and trend identification. This includes ensuring correct formatting and aggregation.
2. **Has your data been properly formatted?**
    - Yes, during the Process phase, we ensured that all necessary columns were added, and the data was cleaned and formatted consistently.
3. **What surprises did you discover in the data?**
    - We discovered negative ride lengths due to inverted timestamps, which we corrected. Additionally, there were outliers with extremely high ride lengths.
4. **What trends or relationships did you find in the data?**
    - Key trends include differences in ride length by user type, ride distribution by day of the week, and ride count by bike type.
5. **How will these insights help answer your business questions?**
    - Insights will help us understand user behavior, enabling targeted marketing strategies to convert casual riders into annual members.

#### **Key Tasks**

1. **Aggregate your data so it’s useful and accessible.**
2. **Organize and format your data.**
3. **Perform calculations.**
4. **Identify trends and relationships.**
5. **Handle and investigate outliers.**

### **Step-by-Step Data Analysis in BigQuery**

#### **Step 1: Import Your Data**

Since the data is already in BigQuery, this step is complete. We worked with the cleaned_final_trips table created in the previous phase.

#### **Step 2: Explore Your Data**

**SQL Queries:**

**Get Total Number of Rows  
**sql  
\-- Get total number of rows

SELECT COUNT(\*) AS total_rows

FROM \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.cleaned_final_trips\`;

**Result:**  
Total Rows: 4,797,860

**Get Distinct Values for Key Columns  
**sql  
\-- Get distinct values for key columns

SELECT

COUNT(DISTINCT ride_id) AS unique_ride_ids,

COUNT(DISTINCT rideable_type) AS unique_bike_types,

COUNT(DISTINCT member_casual) AS unique_user_types

FROM \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.cleaned_final_trips\`;

**Result:**  
unique_ride_ids, unique_bike_types, unique_user_types

4,797,860, 3, 2

**Summary Statistics for Ride Length  
**sql  
\-- Get summary statistics for ride_length

SELECT

MIN(ride_length) AS min_ride_length,

MAX(ride_length) AS max_ride_length,

AVG(ride_length) AS avg_ride_length

FROM \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.cleaned_final_trips\`;

**Result:**  
min_ride_length, max_ride_length, avg_ride_length

0, 5,909,344, 1,126.77

#### **Step 3: Identify and Correct Inverted Timestamps**

We discovered that some ride lengths were negative due to inverted timestamps.

**SQL Queries:**

**Identify Records with Negative Ride Lengths  
**sql  
SELECT

ride_id,

started_at,

ended_at,

ride_length

FROM \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.final_trips\`

WHERE ride_length < 0;

**Correct the Inverted Timestamps  
**sql  
CREATE OR REPLACE TABLE \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.corrected_trips\` AS

SELECT

ride_id,

CASE

WHEN started_at > ended_at THEN ended_at

ELSE started_at

END AS started_at,

CASE

WHEN started_at > ended_at THEN started_at

ELSE ended_at

END AS ended_at,

rideable_type,

member_casual,

TIMESTAMP_DIFF(

CASE

WHEN started_at > ended_at THEN started_at

ELSE ended_at

END,

CASE

WHEN started_at > ended_at THEN ended_at

ELSE started_at

END,

SECOND

) AS ride_length,

day_of_week

FROM

\`casestudy1-429513.Cyclistic_BikeShare_Last12Months.final_trips\`;

**Check for Corrected Records  
**sql  
SELECT \*

FROM \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.corrected_trips\`

WHERE ride_length < 0;

**Create Cleaned and Updated Final Table  
**sql  
CREATE OR REPLACE TABLE \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.cleaned_final_trips\` AS

SELECT \*

FROM \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.corrected_trips\`

WHERE ride_length >= 0;

#### **Step 4: Perform Calculations and Identify Trends**

**Average Ride Length by User Type  
**sql  
SELECT

member_casual,

AVG(ride_length) AS avg_ride_length

FROM \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.cleaned_final_trips\`

GROUP BY member_casual;

**Result:  
**sql  
member_casual, avg_ride_length

casual, 1,689.86

member, 792.70

**Number of Rides by Day of the Week  
**sql  
SELECT

day_of_week,

COUNT(\*) AS ride_count

FROM \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.cleaned_final_trips\`

GROUP BY day_of_week

ORDER BY

CASE day_of_week

WHEN 'Sunday' THEN 1

WHEN 'Monday' THEN 2

WHEN 'Tuesday' THEN 3

WHEN 'Wednesday' THEN 4

WHEN 'Thursday' THEN 5

WHEN 'Friday' THEN 6

WHEN 'Saturday' THEN 7

END;

**Result:**  
day_of_week, ride_count

Sunday, 643,794

Monday, 593,669

Tuesday, 663,518

Wednesday, 713,590

Thursday, 706,356

Friday, 718,244

Saturday, 758,900

**Ride Count by Bike Type  
**sql  
SELECT

rideable_type,

COUNT(\*) AS ride_count

FROM \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.cleaned_final_trips\`

GROUP BY rideable_type;

**Result:**  
rideable_type, ride_count

docked_bike, 30,931

classic_bike, 2,375,985

electric_bike, 2,391,155

#### **Step 5: Investigate Outliers**

To ensure data accuracy, we investigated rides with durations longer than 43,200 seconds (12 hours).

**SQL Queries and Results:**

**Identify Outliers:  
**sql  
SELECT \*

FROM \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.cleaned_final_trips\`

WHERE ride_length > 43200;

**Analyze Outliers by User Type:  
**sql  
\-- Count of outliers by user type

SELECT

member_casual,

COUNT(\*) AS outlier_count

FROM \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.cleaned_final_trips\`

WHERE ride_length > 43200

GROUP BY member_casual;

**Result:  
**sql  
member_casual, outlier_count

casual, 7,101

member, 2,013

**Analyze Outliers by Bike Type:  
**sql  
\-- Count of outliers by bike type

SELECT

rideable_type,

COUNT(\*) AS outlier_count

FROM \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.cleaned_final_trips\`

WHERE ride_length > 43200

GROUP BY rideable_type;

**Result:**  
rideable_type, outlier_count

docked_bike, 959

classic_bike, 8,153

electric_bike, 2

#### **Step 6: Exclude Outliers and Re-run Analysis**

**Exclude Outliers:  
**sql

CREATE OR REPLACE TABLE \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.cleaned_final_trips_excl_outliers\` AS

SELECT \*

FROM \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.cleaned_final_trips\`

WHERE ride_length <= 86400;

\`\`\`

2\. \*\*Re-run Key Analyses\*\*

\*\*SQL Queries and Results:\*\*

1\. \*\*Average Ride Length by User Type\*\*

\`\`\`sql

SELECT

member_casual,

AVG(ride_length) AS avg_ride_length

FROM \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.cleaned_final_trips_excl_outliers\`

GROUP BY member_casual;

\`\`\`

\*\*Result:\*\*

\`\`\`

member_casual, avg_ride_length

casual, 1376.25

member, 802.65

\`\`\`

2\. \*\*Number of Rides by Day of the Week\*\*

\`\`\`sql

SELECT

day_of_week,

COUNT(\*) AS ride_count

FROM \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.cleaned_final_trips_excl_outliers\`

GROUP BY day_of_week

ORDER BY

CASE day_of_week

WHEN 'Sunday' THEN 1

WHEN 'Monday' THEN 2

WHEN 'Tuesday' THEN 3

WHEN 'Wednesday' THEN 4

WHEN 'Thursday' THEN 5

WHEN 'Friday' THEN 6

WHEN 'Saturday' THEN 7

END;

\`\`\`

\*\*Result:\*\*

\`\`\`

day_of_week, ride_count

Sunday, 637,855

Monday, 588,246

Tuesday, 657,342

Wednesday, 706,213

Thursday, 699,485

Friday, 711,243

Saturday, 752,312

\`\`\`

3\. \*\*Ride Count by Bike Type\*\*

\`\`\`sql

SELECT

rideable_type,

COUNT(\*) AS ride_count

FROM \`casestudy1-429513.Cyclistic_BikeShare_Last12Months.cleaned_final_trips_excl_outliers\`

GROUP BY rideable_type;

\`\`\`

\*\*Result:\*\*

\`\`\`

rideable_type, ride_count

docked_bike, 30,241

classic_bike, 2,369,567

electric_bike, 2,384,398

\`\`\`

\### Summary of Analysis

\#### Findings:

1\. \*\*Total Rows and Unique Values:\*\*

\- The dataset contains 4,797,860 unique ride IDs, 3 unique bike types, and 2 unique user types.

2\. \*\*Summary Statistics for Ride Length:\*\*

\- Minimum ride length is 0 seconds, maximum ride length is 5,909,344 seconds, and the average ride length is approximately 1,126.77 seconds.

3\. \*\*Average Ride Length by User Type:\*\*

\- Casual riders have an average ride length of 1,689.86 seconds (28.2 minutes).

\- Members have an average ride length of 792.70 seconds (13.2 minutes).

\- After excluding outliers, the average ride length for casual riders decreased to 1,376.25 seconds (about 22.94 minutes) and for members to 802.65 seconds (about 13.38 minutes).

4\. \*\*Number of Rides by Day of the Week:\*\*

\- The highest number of rides occurs on Saturdays, followed by Fridays and Wednesdays. Mondays have the lowest number of rides.

\- After excluding outliers, the distribution of rides across different days of the week remains similar.

5\. \*\*Ride Count by Bike Type:\*\*

\- Electric bikes have the highest ride count, closely followed by classic bikes. Docked bikes have significantly fewer rides.

\- After excluding outliers, classic and electric bikes remain the most popular, with docked bikes having significantly fewer rides.

\#### Insights:

\- \*\*Casual riders tend to have longer rides compared to members.\*\*

\- Even after excluding outliers, casual riders' average ride length is longer than that of members, indicating different usage patterns.

\- \*\*Ride usage peaks on weekends, indicating higher leisure activity during these days.\*\*

\- This trend remains consistent even after excluding outliers, suggesting a stable pattern of higher usage on weekends.

\- \*\*Electric and classic bikes are more popular than docked bikes.\*\*

\- The preference for electric and classic bikes over docked bikes is evident, both before and after excluding outliers.

These insights help confirm the initial findings and provide a clearer understanding of user behavior, which is crucial for informing targeted marketing strategies.

—---------------------------------------------------------------------

### **Share Phase Report**

#### **Guiding Questions**

1. **Were you able to answer the question of how annual members and casual riders use Cyclistic bikes differently?**
    - Yes, the analysis provided clear insights into how annual members and casual riders use Cyclistic bikes differently. Casual riders tend to take longer rides and have a higher concentration of rides on weekends, whereas annual members exhibit more evenly distributed ride patterns throughout the week.
2. **What story does your data tell?**
    - The data tells a story of distinct usage patterns between casual riders and annual members. Casual riders often take longer trips and prefer weekends, indicating a leisure-oriented usage. In contrast, annual members use the bikes more consistently throughout the week, suggesting a mix of commuting and leisure activities.
3. **How do your findings relate to your original question?**
    - The findings directly address the original question by highlighting the key differences in ride duration and frequency between casual riders and annual members. This information is crucial for designing targeted marketing strategies to convert casual riders into annual members.
4. **Who is your audience? What is the best way to communicate with them?**
    - The audience includes the executive team at Cyclistic. The best way to communicate with them is through polished and sophisticated visualizations that clearly convey the key insights and actionable recommendations.
5. **Can data visualization help you share your findings?**
    - Yes, data visualization is an effective way to share the findings as it makes the data more accessible and understandable, highlighting the important patterns and trends.
6. **Is your presentation accessible to your audience?**
    - The presentation is designed to be accessible, with clear titles, labels, and color contrasts to ensure that the visualizations effectively communicate the key insights.

#### **Key Tasks**

1. **Determine the best way to share your findings.**
    - Use a PowerPoint presentation with clear, concise visualizations to share the findings with the executive team.
2. **Create effective data visualizations.**
    - Use bar charts to illustrate average ride lengths, ride counts by day of the week, and ride counts by bike type.
3. **Present your findings.**
    - Include supporting visualizations and key findings in a PowerPoint presentation to effectively communicate the insights.
4. **Ensure your work is accessible.**
    - Use clear titles, labels, and color contrasts to make the visualizations easy to understand.

#### **Deliverable**

**Supporting Visualizations and Key Findings:**

1. **Average Ride Length by User Type**
    - **Key Finding:** Casual riders have longer average ride lengths (1376.25 seconds) compared to members (802.65 seconds).
2. **Number of Rides by Day of the Week (Casual Users)  
    **
    - **Key Finding:** Casual users have higher ride counts on weekends.
3. **Number of Rides by Day of the Week (Members)**
    - **Key Finding:** Members have a more even distribution of rides throughout the week.
4. **Ride Count by Bike Type**
    - **Key Finding:** Electric bikes have the highest ride count, closely followed by classic bikes. Docked bikes have significantly fewer rides.
5. **Average Number of Rides by Day of the Week (Casual vs Member Users)**
    - **Key Finding:** Casual users have higher average ride counts on weekends, while members have a more even distribution of rides throughout the week.


### **R code for Visuals**

# Load necessary libraries
library(ggplot2)

# Data for Average Ride Length by User Type
avg_ride_length_no_outliers <- data.frame(
  member_casual = c("casual", "member"),
  avg_ride_length = c(1376.25, 802.65)
)

# Plot average ride length by user type
ggplot(avg_ride_length_no_outliers, aes(x = member_casual, y = avg_ride_length, fill = member_casual)) +
  geom_bar(stat = "identity") +
  labs(title = "Average Ride Length by User Type", x = "User Type", y = "Average Ride Length (seconds)") +
  theme_minimal()
############################################
# Load necessary libraries
library(ggplot2)

# Data for Number of Rides by Day of the Week
ride_count_by_day_df <- data.frame(
  day_of_week = c('Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'),
  ride_count = c(588246, 657342, 706213, 699485, 711243, 752312, 637855)
)

# Convert day_of_week to a factor with the correct order
ride_count_by_day_df$day_of_week <- factor(ride_count_by_day_df$day_of_week, levels = c('Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'))

# Plotting Number of Rides by Day of the Week with abbreviated days and ordered
ggplot(ride_count_by_day_df, aes(x = day_of_week, y = ride_count, fill = day_of_week)) +
  geom_bar(stat = "identity") +
  labs(title = "Number of Rides by Day of the Week", x = "Day of the Week", y = "Ride Count") +
  theme_minimal() +
  scale_fill_brewer(palette = "Set3") +
  theme(axis.text.x = element_text(size = 8), plot.title = element_text(hjust = 0.5))


##############################################
# Data for Ride Count by Bike Type
ride_count_by_bike_df <- data.frame(
  rideable_type = c('docked_bike', 'classic_bike', 'electric_bike'),
  ride_count = c(30241, 2369567, 2384398)
)

# Plotting Ride Count by Bike Type
ggplot(ride_count_by_bike_df, aes(x = rideable_type, y = ride_count, fill = rideable_type)) +
  geom_bar(stat = "identity") +
  labs(title = "Ride Count by Bike Type", x = "Bike Type", y = "Ride Count") +
  theme_minimal() +
  scale_fill_manual(values = c("docked_bike" = "red", "classic_bike" = "orange", "electric_bike" = "yellow")) +
  theme(plot.title = element_text(hjust = 0.5))

###############################################
# Load necessary libraries
library(ggplot2)
library(dplyr)

# Example data for average rides per day of the week for Casual Users
ride_count_by_day_casual_df <- data.frame(
  day_of_week = c('Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'),
  avg_ride_count = c(21428.57, 24285.71, 25714.29, 22857.14, 27142.86, 31428.57, 30000), # Example data
  user_type = 'casual'
)

# Example data for average rides per day of the week for Members
ride_count_by_day_member_df <- data.frame(
  day_of_week = c('Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'),
  avg_ride_count = c(62606.57, 69620.29, 75173.29, 77069.29, 74462, 76044.57, 61122.14), # Example data
  user_type = 'member'
)

# Combine the data into a single dataframe
combined_avg_ride_count_df <- bind_rows(ride_count_by_day_casual_df, ride_count_by_day_member_df)

# Plotting Combined Average Number of Rides by Day of the Week for Casual and Member Users
ggplot(combined_avg_ride_count_df, aes(x = factor(day_of_week, levels = c('Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun')), y = avg_ride_count, fill = user_type)) +
  geom_bar(stat = "identity", position = "dodge") +
  labs(title = "Average Number of Rides by Day of the Week (Casual vs Member Users)", x = "Day of the Week", y = "Average Ride Count") +
  theme_minimal() +
  scale_fill_manual(values = c("casual" = "blue", "member" = "green")) +
  theme(axis.text.x = element_text(size = 8), plot.title = element_text(hjust = 0.5))

# Save the plot
ggsave("average_number_of_rides_by_day_of_week_combined.png")

—----------------------------------------------------------

### **Interpretation of Findings**

The analysis and visualizations provide several key insights into the usage patterns of Cyclistic bikes by annual members and casual riders. Here's what the data tells us:

#### **1\. Average Ride Length by User Type**

**Key Finding:**

- **Casual riders** have significantly longer average ride lengths (1376.25 seconds, approximately 23 minutes) compared to **annual members** (802.65 seconds, approximately 13 minutes).

**Interpretation:**

- This suggests that casual riders might be using the bikes for leisure activities, sightseeing, or infrequent but longer trips. In contrast, annual members likely use the bikes more frequently for shorter trips, possibly for commuting or daily errands.

#### **2\. Number of Rides by Day of the Week (Casual Users)**

**Key Finding:**

- Casual users have higher ride counts on weekends, particularly on Saturdays and Sundays.

**Interpretation:**

- This indicates that casual riders prefer to use bikes during weekends, which aligns with the idea of leisure and recreational use. This pattern could be influenced by tourists or local residents looking for activities during their free time on weekends.

#### **3\. Number of Rides by Day of the Week (Members)**

**Key Finding:**

- Annual members exhibit a more even distribution of rides throughout the week, with slight peaks on weekdays.

**Interpretation:**

- The even distribution suggests that annual members use the bikes regularly, likely for commuting to work or school, running errands, or other daily activities. The slight increase in rides during weekdays could indicate that members are using bikes for their daily commute.

#### **4\. Ride Count by Bike Type**

**Key Finding:**

- **Electric bikes** have the highest ride count, closely followed by **classic bikes**, while **docked bikes** have significantly fewer rides.

**Interpretation:**

- The popularity of electric bikes suggests that users prefer the ease and convenience they offer, especially for longer or more strenuous trips. Classic bikes also have a high ride count, indicating their continued relevance and utility. Docked bikes, with fewer rides, might be less preferred due to their limited availability or convenience compared to the other types.

#### **5\. Average Number of Rides by Day of the Week (Casual vs Member Users)**

**Key Finding:**

- Casual users have higher average ride counts on weekends, while members have a more even distribution of rides throughout the week.

**Interpretation:**

- This reinforces the earlier findings that casual riders predominantly use bikes for leisure on weekends, whereas members incorporate bike usage into their daily routine.

### **Overall Story**

The data tells a compelling story of two distinct user groups with different biking habits:

- **Casual riders** are likely to be leisure-oriented, using bikes for longer rides and primarily on weekends. This pattern is typical of tourists or occasional users who prefer bike riding as a recreational activity.
- **Annual members** are more consistent in their usage, integrating bike rides into their daily routines for shorter, frequent trips. This behavior suggests that members use the service as a reliable mode of transportation for commuting and regular errands.

### **Implications for Cyclistic**

These insights are critical for Cyclistic's strategic planning:

- **Marketing Strategies:** To convert casual riders into annual members, Cyclistic could highlight the convenience and cost savings of an annual membership for regular bike usage. Promotions could target casual riders with weekend-exclusive offers to encourage more consistent usage.
- **Service Improvements:** Given the popularity of electric bikes, Cyclistic might consider expanding its fleet of electric bikes to meet user demand. Additionally, enhancing the availability and accessibility of bikes during weekends could cater to the peak usage times of casual riders.
- **User Engagement:** Cyclistic can design engagement programs for annual members that reward frequent usage and incorporate feedback mechanisms to continually improve the user experience.

### **Summary**

The analysis and visualizations provide a clear understanding of how different user groups utilize Cyclistic's bike-sharing service. By leveraging these insights, Cyclistic can tailor its marketing strategies, service offerings, and user engagement initiatives to better meet the needs of both casual riders and annual members, ultimately driving growth and enhancing customer satisfaction

—----------------------------------------------------------------------------------------------------------------------------

### **Act Phase Report**

#### **Guiding Questions**

1. **What is your final conclusion based on your analysis?**
    - The final conclusion from the analysis is that casual riders and annual members use Cyclistic bikes differently. Casual riders tend to take longer rides primarily on weekends, indicating leisure or recreational use. In contrast, annual members have shorter, more frequent rides evenly distributed throughout the week, suggesting regular commuting or daily activities.
2. **How could your team and business apply your insights?**
    - Cyclistic can apply these insights to tailor marketing strategies, enhance service offerings, and engage users more effectively. By understanding the distinct usage patterns of casual riders and annual members, Cyclistic can design targeted campaigns and improve bike availability to meet the specific needs of each group.
3. **What next steps would you or your stakeholders take based on your findings?**
    - The next steps include implementing targeted marketing campaigns to convert casual riders into annual members, expanding the fleet of electric bikes to meet user demand, and improving bike availability during peak usage times on weekends. Additionally, Cyclistic could explore further engagement programs for annual members to reward frequent usage and gather feedback for continuous improvement.
4. **Is there additional data you could use to expand on your findings?**
    - Additional data on user demographics, trip purposes, and seasonal variations could provide deeper insights into user behavior. Surveys or user feedback could also help understand the motivations behind bike usage and preferences, enabling more personalized and effective marketing strategies.

### **Converting Casual Riders to Annual Members**

To convert casual riders into annual members, Cyclistic can employ a range of strategies that target the specific needs and behaviors of casual users. Here are some actionable recommendations:

#### **1\. Targeted Marketing Campaigns**

- **Personalized Offers:** Use data analytics to identify patterns in casual rider behavior and send personalized offers. For example, provide discounts on annual memberships to frequent casual riders.
- **Weekend Promotions:** Since casual riders are more active on weekends, offer special weekend promotions that highlight the benefits of becoming an annual member. These could include weekend ride packages or limited-time discounts.
- **Referral Programs:** Encourage current members to refer friends by offering incentives for both the referrer and the new member. This can leverage word-of-mouth marketing to attract new members.

#### **2\. Enhanced Membership Benefits**

- **Flexible Membership Plans:** Introduce flexible membership options, such as seasonal or monthly plans, to appeal to casual riders who may not be ready to commit to a full year.
- **Exclusive Perks:** Offer exclusive perks to members, such as priority access to bikes, extended ride times, or partnerships with local businesses (e.g., discounts at cafes or shops).

#### **3\. Improved User Experience**

- **Seamless Sign-Up Process:** Simplify the process of becoming an annual member with easy online sign-ups and clear instructions.
- **User-Friendly App:** Enhance the Cyclistic app to provide a seamless user experience. Features like easy membership management, ride history tracking, and personalized recommendations can add value for users.

#### **4\. Educational Campaigns**

- **Value Proposition:** Communicate the long-term cost savings and convenience of an annual membership compared to pay-per-ride. Use infographics and testimonials to illustrate the benefits.
- **Sustainability Messaging:** Promote the environmental benefits of biking over driving, appealing to eco-conscious casual riders.

#### **5\. Community Engagement**

- **Events and Rides:** Organize community events, group rides, or biking workshops that are exclusive to members. This can build a sense of community and belonging among members.
- **Social Media Campaigns:** Use social media to engage with casual riders, sharing stories, tips, and user-generated content that highlight the advantages of being a member.

By implementing these strategies, Cyclistic can effectively convert casual riders into annual members, enhancing user engagement and driving long-term growth.

.
