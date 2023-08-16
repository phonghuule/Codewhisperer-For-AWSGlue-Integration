# **Building data integration faster with Amazon CodeWhisperer for AWS Glue**

This lab is provided as part of **[AWS Innovate Data Edition](https://aws.amazon.com/events/aws-innovate/apj/data/)**

ℹ️ You will run this lab in your own AWS account. Please follow directions at the end of the lab to remove resources to avoid future costs.

## Introduction

Amazon CodeWhisperer is an AI coding companion that uses foundational models under the hood to improve developer productivity. 

In this lab, learn how AWS Glue Studio notebook integration with Amazon CodeWhisperer helps you build data integration jobs faster.

## Prerequisites
Before going forward with this lab, you need to complete the following prerequisites:

1. [Set up AWS Glue Studio](https://docs.aws.amazon.com/glue/latest/ug/setting-up.html).
2. Configure an [AWS Identity and Access Management (IAM)](https://aws.amazon.com/iam/) role to interact with Amazon CodeWhisperer. Attach the following policy to your IAM role for the AWS Glue Studio notebook: 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "CodeWhispererPermissions",
            "Effect": "Allow",
            "Action": [
                "codewhisperer:GenerateRecommendations"
            ],
            "Resource": "*"
        }
    ]
}
```

## Getting Started
1. On the AWS Glue console, choose **Notebooks** under **ETL jobs** in the navigation pane.
2. Select **Jupyter Notebook** and choose **Create**.
3. For **Job name**, enter **codewhisperer-demo**.
4. For **IAM Role**, select your IAM role that you configured as a prerequisite.
5. Choose **Start notebook**.

A new notebook is created with sample cells.

![](/Images/BDB-3499-image003.jpg)

At the bottom, there is a menu named **CodeWhisperer**. By choosing this menu, you can see the shortcuts and several options, including disabling auto-suggestions.

![](/Images/BDB-3499-image005-233x300.jpg)

Let’s try your first recommendation by Amazon CodeWhisperer. Note that this post contains examples of recommendations, but you may see different code snippets recommended by Amazon CodeWhisperer.

Add a new cell and enter your comment to describe what you want to achieve. After you press Enter, the recommended code is shown.

![](/Images/BDB-3499-image007.jpg)

If you press **Tab**, then code is chosen. If you press arrow keys, then you can select other recommendations. You can learn more in [User actions](https://docs.aws.amazon.com/codewhisperer/latest/userguide/actions-and-shortcuts.html).


Now let’s read a JSON file from [Amazon Simple Storage Service (Amazon S3)](https://aws.amazon.com/s3/). Enter the following code comment into a notebook cell and press **Enter**:

```
# Create a Spark DataFrame from a json file
```

CodeWhisperer will recommend a code snippet similar to the following:
```
def create_spark_df_from_json(spark, file_path):
    return spark.read.json(file_path)
```

Now use this method to utilize the suggested code snippet:
```
df = create_spark_df_from_json(spark, "s3://awsglue-datasets/examples/us-legislators/all/persons.json")
df.show()
```

The proceeding code returns the following output:

As you can see from the result, you can quickly utilize the code snippet recommended by Amazon CodeWhisperer.

## Examples of code recommendations

In this section, we provide additional examples of code recommendations. Note that these are just our examples, and different code snippets may be suggested by Amazon CodeWhisperer.

### Add a column with a calculation

In extract, transform, and load (ETL) use cases, it’s common to add new columns from existing columns. When we need to add columns to our Spark DataFrame, we can articulate with a high level of detail to Amazon CodeWhisperer what type of column we need added and its respective attributes:

```
# Add age column to a given person DataFrame
# age is calculated from current date and birth_date. When death_date is not null, then use death_date to calculate age
```

Amazon CodeWhisperer will recommend a code snippet similar to the following:

```
def add_age_column(df):
    # Use current date to calculate age
    current_date = current_timestamp()
    # Use birth_date column to calculate age
    df = df.withColumn("age", datediff(current_date, df.birth_date) / 365)
    # Use death_date column to calculate age
    df = df.withColumn("age", when(df.death_date.isNull(), df.age).otherwise(datediff(df.death_date, df.birth_date) / 365))
    return df
```

It can be utilized as follows:

```
df = add_age_column(df)
df.show()
```

### Sort and extract records
You can use Amazon CodeWhisperer for sorting data and extracting records within a Spark DataFrame as well:
```
# Show top 5 oldest persons from DataFrame
# Use age column
```

Amazon CodeWhisperer will recommend a code snippet similar to the following:
```
def get_oldest_person(df):
    return df.orderBy(desc("age")).limit(5)
```

It can be utilized as follows:
```
get_oldest_person(df).show()
```

### Generate sample datasets in a Spark DataFrame
Amazon CodeWhisperer is powerful enough to generate sample Spark DataFrames as well, which can be done like so:
```
# Generate sample Spark DataFrame of country name and country code
# First column name is country_name, and second column name is country_code
```

Amazon CodeWhisperer will recommend a code snippet similar to the following:
```
def get_country_code_df(spark):
    return spark.createDataFrame(
        [("United States", "US"), ("United Kingdom", "UK"), ("Canada", "CA")],
        ["country_name", "country_code"]
    )
```

It can be utilized as follows:
```
df = get_country_code_df(spark)
df.show()
```

### Generate transformations in SQL
We can also use Amazon CodeWhisperer to create a code snippet for transformation in SQL and create a new table from the SQL query results (CTAS) like so:
```
# Generate CTAS query by selecting all the records in a table with grouping by a given column
```

Amazon CodeWhisperer will recommend a code snippet similar to following:
```
def generate_ctas_query_with_group_by(table_name, group_by_col):
    ctas_query = "CREATE TABLE " + table_name + " AS SELECT * FROM " + table_name + " GROUP BY " + group_by_col
    return ctas_query
```

## Tear Down

### Delete the notebook instance
1. Navigate to the [Amazon SageMaker console](https://console.aws.amazon.com/sagemaker/home) 
2. Choose **Notebook instances**.
3. Select your notebook instance in the Notebook instances table. In the **Actions** menu, select **Stop**.
4. After the Status changes to 'Stopped', choose **Delete** in the Actions menu. Confirm by choosing Delete in the confirmation modal.

## Survey
Let us know what you thought of this session and how we can improve the presentation experience for you in the future by completing this [event session poll](https://amazonmr.au1.qualtrics.com/jfe/form/SV_1U4cxprfqLngWGy?Session=HOL05). Participants who complete the surveys from AWS Innovate Online Conference will receive a gift code for USD25 in AWS credits1, 2 & 3. AWS credits will be sent via email by September 29, 2023.
Note: Only registrants of AWS Innovate Online Conference who complete the surveys will receive a gift code for USD25 in AWS credits via email.

<sup>1</sup>AWS Promotional Credits Terms and conditions apply: https://aws.amazon.com/awscredits/ 

<sup>2</sup>Limited to 1 x USD25 AWS credits per participant.

<sup>3</sup>Participants will be required to provide their business email addresses to receive the gift code for AWS credits.

