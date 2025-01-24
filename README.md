# Walmart Data Analysis: End-to-End SQL + Python Project 1

## Project Overview

![Project Pipeline](https://github.com/najirh/Walmart_SQL_Python/blob/main/walmart_project-piplelines.png)


This project is an end-to-end data analysis solution designed to extract critical business insights from Walmart sales data. We utilize Python for data processing and analysis, SQL for advanced querying, and structured problem-solving techniques to solve key business questions. The project is ideal for data analysts looking to develop skills in data manipulation, SQL querying, and data pipeline creation.

---

## Project Steps

### 1. Set Up the Environment
   - **Tools Used**: Visual Studio Code (VS Code), Python, SQL (MySQL and PostgreSQL)
   - **Goal**: Create a structured workspace within VS Code and organize project folders for smooth development and data handling.

### 2. Set Up Kaggle API
   - **API Setup**: Obtain your Kaggle API token from [Kaggle](https://www.kaggle.com/) by navigating to your profile settings and downloading the JSON file.
   - **Configure Kaggle**: 
      - Place the downloaded `kaggle.json` file in your local `.kaggle` folder.
      - Use the command `kaggle datasets download -d <dataset-path>` to pull datasets directly into your project.

### 3. Download Walmart Sales Data
   - **Data Source**: Use the Kaggle API to download the Walmart sales datasets from Kaggle.
   - **Dataset Link**: [Walmart Sales Dataset](https://www.kaggle.com/najir0123/walmart-10k-sales-datasets)
   - **Storage**: Save the data in the `data/` folder for easy reference and access.

### 4. Install Required Libraries and Load Data
   - **Libraries**: Install necessary Python libraries using:
     ```bash
     pip install pandas numpy sqlalchemy mysql-connector-python psycopg2
     ```
   - **Loading Data**: Read the data into a Pandas DataFrame for initial analysis and transformations.

### 5. Explore the Data
   - **Goal**: Conduct an initial data exploration to understand data distribution, check column names, types, and identify potential issues.
   - **Analysis**: Use functions like `.info()`, `.describe()`, and `.head()` to get a quick overview of the data structure and statistics.

### 6. Data Cleaning
   - **Remove Duplicates**: Identify and remove duplicate entries to avoid skewed results.
   - **Handle Missing Values**: Drop rows or columns with missing values if they are insignificant; fill values where essential.
   - **Fix Data Types**: Ensure all columns have consistent data types (e.g., dates as `datetime`, prices as `float`).
   - **Currency Formatting**: Use `.replace()` to handle and format currency values for analysis.
   - **Validation**: Check for any remaining inconsistencies and verify the cleaned data.

### 7. Feature Engineering
   - **Create New Columns**: Calculate the `Total Amount` for each transaction by multiplying `unit_price` by `quantity` and adding this as a new column.
   - **Enhance Dataset**: Adding this calculated field will streamline further SQL analysis and aggregation tasks.

### 8. Load Data into MySQL and PostgreSQL
   - **Set Up Connections**: Connect to MySQL and PostgreSQL using `sqlalchemy` and load the cleaned data into each database.
   - **Table Creation**: Set up tables in both MySQL and PostgreSQL using Python SQLAlchemy to automate table creation and data insertion.
   - **Verification**: Run initial SQL queries to confirm that the data has been loaded accurately.

### 9. SQL Analysis: Complex Queries and Business Problem Solving
   - **Business Problem-Solving**: Write and execute complex SQL queries to answer critical business questions, such as:
     - Revenue trends across branches and categories.
     - Identifying best-selling product categories.
     - Sales performance by time, city, and payment method.
     - Analyzing peak sales periods and customer buying patterns.
     - Profit margin analysis by branch and category.
   - **Documentation**: Keep clear notes of each query's objective, approach, and results.

### 10. Project Publishing and Documentation
   - **Documentation**: Maintain well-structured documentation of the entire process in Markdown or a Jupyter Notebook.
   - **Project Publishing**: Publish the completed project on GitHub or any other version control platform, including:
     - The `README.md` file (this document).
     - Jupyter Notebooks (if applicable).
     - SQL query scripts.
     - Data files (if possible) or steps to access them.

---

## Requirements

- **Python 3.8+**
- **SQL Databases**: MySQL, PostgreSQL
- **Python Libraries**:
  - `pandas`, `numpy`, `sqlalchemy`, `mysql-connector-python`, `psycopg2`
- **Kaggle API Key** (for data downloading)

## Getting Started

1. Clone the repository:
   ```bash
   git clone <repo-url>
   ```
2. Install Python libraries:
   ```bash
   pip install -r requirements.txt
   ```
3. Set up your Kaggle API, download the data, and follow the steps to load and analyze.

---

## Project Structure

```plaintext
|-- data/                     # Raw data and transformed data
|-- sql_queries/              # SQL scripts for analysis and queries
|-- notebooks/                # Jupyter notebooks for Python analysis
|-- README.md                 # Project documentation
|-- requirements.txt          # List of required Python libraries
|-- main.py                   # Main script for loading, cleaning, and processing data
```
---

## Results and Insights

This section will include your analysis findings:
- **Sales Insights**: Key categories, branches with highest sales, and preferred payment methods.
- **Profitability**: Insights into the most profitable product categories and locations.
- **Customer Behavior**: Trends in ratings, payment preferences, and peak shopping hours.

## Future Enhancements

Possible extensions to this project:
- Integration with a dashboard tool (e.g., Power BI or Tableau) for interactive visualization.
- Additional data sources to enhance analysis depth.
- Automation of the data pipeline for real-time data ingestion and analysis.

---

## License

This project is licensed under the MIT License. 

---

## Acknowledgments

- **Data Source**: Kaggle’s Walmart Sales Dataset
- **Inspiration**: Walmart’s business case studies on sales and supply chain optimization.

---

SELECT * FROM walmart_db.walmart;

select * from walamrt;







-- business problem;
-- Q1. find the different payment mehod and number of transaction ,number of quantity sold?

select
	  payment_method,
      count(*),
      sum(quantity) as no_qty_sld
from walmart
group by payment_method;


-- project question #2
-- identify the highest-rated category in each branch,displaying the branch, category 
-- AVG RATING?
	
 select *
 from (   
	 select
		 branch,
		 category ,
		 avg(rating) as avg_rating,
		 rank() over(partition by branch order by avg(rating) desc) as ranking
	 from walmart
	 group by branch , category
) ranked_data
where ranking = 1;
    

-- q3.identify the busiest day for the each branch based on the number of transactions

select *
from (
	SELECT
		branch,
		DAYNAME(STR_TO_DATE(date, '%d/%m/%y')) AS formatted_date,
		count(*) as no_transaction,
		rank() over (partition by branch order by count(*) desc) as ranking
	FROM walmart
	group by 1, 2
) ranked_data
where ranking = 1;


-- q4 different payment method of transaction and no_qty_sold for each payment method

select
	  payment_method,
      count(*),
      sum(quantity) as no_qty_sold
from walmart
group by payment_method;

-- q5. determine the avg ,min, max rating for the each city
-- list the city

select * from walmart_db.walmart;

select
    city,
    category,
    min(rating) as min_rating,
    max(rating) as max_rating,
    avg(rating) as avg_rating
from walmart
group by 1, 2;

-- q6 calculate the total profit for each catagory by considering total_profit as (unit_price * quantity * profit_margin). 
-- list category and total_profit,ordered from highest to lowest profit.

select
	category,
    sum(total) as total_revenue,
    sum(total * profit_margin) as profit
from walmart
group by 1;

-- q7  determine the most common payment method for each branch.
-- display branch and the preferred_payment method.

with cte
as
(select
    branch,
    payment_method,
    count(*) as total_trans,
    rank() over(partition by branch order by count(*) desc) as rank_
from walmart
group by 1, 2
)
select *
from cte
where rank_ = 1;

-- q8 categorize the sales into 3 group MORNING, AFTERNOON, EVENING
-- find out each of the shift and number of invoices.

SELECT TIME(time) FROM walmart;

SELECT
     branch,
    CASE
        WHEN HOUR(time) < 12 THEN 'morning'
        WHEN HOUR(time) BETWEEN 12 AND 17 THEN 'afternoon'
        ELSE 'evening'
    END AS day_time,
    COUNT(*) as transaction_count
FROM walmart
GROUP BY branch, day_time
order by branch, transaction_count desc;



-- q9 idetify the 5 branch with highest decrease ratio 
-- revenue compare to last year(current year 2023 and last year 2022)
-- rdr(revenue decrease ratio(last_rev - current-rev)*100


-- 2022 sales
with revenue_2022
as
(
	SELECT
		branch,
		sum(total) as revenue
	FROM walmart
	WHERE YEAR(STR_TO_DATE(date, '%d/%m/%y')) = 2022
	group by 1
),

revenue_2023
as
(
    SELECT
		branch,
		sum(total) as revenue
	FROM walmart
	WHERE YEAR(STR_TO_DATE(date, '%d/%m/%y')) = 2023
	group by 1     
)
select
	ls.branch,
    ls.revenue as last_year_revenue,
    cs.revenue as cr_year_revenue,
    round((ls.revenue - cs.revenue) / ls.revenue * 100,2) as revenue_decrease_ratio
from revenue_2022 as ls
join
revenue_2023 as cs
on ls.branch = cs.branch
where
	ls.revenue > cs.revenue
order by 4 desc
limit 5

-- well done

