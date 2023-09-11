# **Building data integration faster with Amazon CodeWhisperer for AWS Glue**

This lab is provided as part of **[AWS Innovate Data Edition](https://aws.amazon.com/events/aws-innovate/apj/data/)**

ℹ️ You will run this lab in your own AWS account. Please follow directions at the end of the lab to remove resources to avoid future costs.

## Introduction

Amazon CodeWhisperer is an AI coding companion that uses foundational models under the hood to improve developer productivity. 

In this lab, learn how AWS Glue Studio notebook integration with Amazon CodeWhisperer helps you build data integration jobs faster.

## Prerequisites
Before going forward with this lab, you need to complete the following prerequisites:

1. [Set up AWS Glue Studio](https://docs.aws.amazon.com/glue/latest/ug/setting-up.html).
2. Navigate to [IAM Console](https://us-east-1.console.aws.amazon.com/iamv2/home?region=us-east-1#/policies) and select **Create policy**
    
![IAM Create Policy](/Images/IAM-Create-Policy.png)

3. Choose **JSON**, Copy and Paste the following JSON document in the IAM policy and click Next

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "glue:GetTableVersions",
                "glue:GetPartitions",
                "glue:GetDevEndpoint",
                "glue:GetJobs",
                "s3:GetBucketWebsite",
                "s3:GetMultiRegionAccessPoint",
                "s3:GetObjectAttributes",
                "s3:GetObjectLegalHold",
                "s3:GetBucketNotification",
                "s3:DescribeMultiRegionAccessPointOperation",
                "s3:GetReplicationConfiguration",
                "glue:GetPartition",
                "glue:DeleteConnection",
                "glue:BatchDeleteConnection",
                "s3:GetStorageLensDashboard",
                "s3:GetLifecycleConfiguration",
                "s3:GetInventoryConfiguration",
                "s3:GetBucketTagging",
                "s3:GetAccessPointPolicyForObjectLambda",
                "glue:BatchDeletePartition",
                "glue:CreateUserDefinedFunction",
                "s3:ListBucket",
                "glue:DeleteJob",
                "codewhisperer:GenerateRecommendations",
                "glue:CreateJob",
                "iam:PassRole",
                "glue:GetConnection",
                "glue:ResetJobBookmark",
                "glue:CreatePartition",
                "glue:UpdatePartition",
                "s3:GetMultiRegionAccessPointPolicyStatus",
                "glue:BatchGetPartition",
                "s3:GetBucketVersioning",
                "s3:GetAccessPointConfigurationForObjectLambda",
                "glue:GetTable",
                "glue:GetDatabase",
                "s3:GetMultiRegionAccessPointRoutes",
                "s3:GetStorageLensConfiguration",
                "s3:GetAccountPublicAccessBlock",
                "s3:ListAllMyBuckets",
                "glue:CreateDatabase",
                "s3:GetBucketCORS",
                "s3:GetObjectVersion",
                "glue:BatchCreatePartition",
                "s3:GetObjectVersionTagging",
                "glue:UpdateTable",
                "glue:DeleteTable",
                "s3:GetStorageLensConfigurationTagging",
                "s3:GetObjectAcl",
                "s3:GetBucketObjectLockConfiguration",
                "s3:GetIntelligentTieringConfiguration",
                "glue:GetUserDefinedFunction",
                "s3:GetObjectVersionAcl",
                "glue:GetUserDefinedFunctions",
                "s3:GetBucketPolicyStatus",
                "glue:UpdateDatabase",
                "s3:GetObjectRetention",
                "glue:CreateTable",
                "glue:GetTables",
                "s3:GetJobTagging",
                "glue:DeleteUserDefinedFunction",
                "glue:CreateConnection",
                "s3:GetObject",
                "glue:GetDevEndpoints",
                "s3:DescribeJob",
                "glue:BatchDeleteTable",
                "s3:GetAnalyticsConfiguration",
                "s3:GetObjectVersionForReplication",
                "glue:DeletePartition",
                "s3:GetAccessPointForObjectLambda",
                "glue:GetJob",
                "glue:GetConnections",
                "s3:GetAccessPoint",
                "glue:DeleteDatabase",
                "s3:GetBucketLogging",
                "s3:GetAccelerateConfiguration",
                "s3:GetObjectVersionAttributes",
                "s3:GetBucketPolicy",
                "glue:*",
                "s3:GetEncryptionConfiguration",
                "s3:GetObjectVersionTorrent",
                "s3:GetBucketRequestPayment",
                "s3:GetAccessPointPolicyStatus",
                "s3:GetObjectTagging",
                "glue:UpdateJob",
                "s3:GetMetricsConfiguration",
                "s3:GetBucketOwnershipControls",
                "glue:GetJobBookmark",
                "s3:GetBucketPublicAccessBlock",
                "glue:UpdateUserDefinedFunction",
                "s3:GetMultiRegionAccessPointPolicy",
                "s3:GetAccessPointPolicyStatusForObjectLambda",
                "glue:GetDatabases",
                "s3:GetBucketAcl",
                "s3:GetObjectTorrent",
                "glue:UpdateConnection",
                "glue:UpdateDevEndpoint",
                "s3:GetBucketLocation",
                "s3:GetAccessPointPolicy"
            ],
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": "s3:GetObject",
            "Resource": [
                "arn:aws:s3:::crawler-public*",
                "arn:aws:s3:::aws-glue*"
            ]
        },
        {
            "Sid": "VisualEditor2",
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:DeleteObject"
            ],
            "Resource": "arn:aws:s3:::aws-glue*"
        }
    ]
}
```
![IAM Policy](/Images/IAM-Policy.png)

4. Name Policy as **Glue-CodeWhisperer-GenerateRecommendations-Policy** and **Create Policy**

![Create Policy](/Images/create-policy.png)

5. Navigate to [IAM Roles](https://us-east-1.console.aws.amazon.com/iamv2/home?region=us-east-1#/roles) and choose **Create Role**

6. Select **Glue** as Trusted Entity and click **Next**

![Trusted Entity](/Images/Trusted-Entity.png)

7. Select **Glue-CodeWhisperer-GenerateRecommendations-Policy** in the **Permissions Policies** and click Next

![Permission Policies](/Images/Permissions-Policy.png)

8. Name the role as **Glue-CodeWhisperer-GenerateRecommendations-Role** and click on **Create Role**

![Create Role](/Images/Create-Role.png)

## Getting Started
1. Naviagate to the [AWS Glue Studio Console](https://us-east-1.console.aws.amazon.com/gluestudio/home?region=us-east-1#/jobs)
2. Select **Jupyter Notebook** and choose **Create**.

![Create Notebook](/Images/Create-Notebook.png)

3. For **Job name**, enter **codewhisperer-demo**.
4. For **IAM Role**, select your IAM role that you configured as a prerequisite.
5. Choose **Start notebook**.

![Notebook Setup](/Images/Notebook-Setup.png)

A new notebook is created with sample cells.

![](/Images/BDB-3499-image003.jpg)

At the bottom, there is a menu named **CodeWhisperer**. By choosing this menu, you can see the shortcuts and several options, including disabling auto-suggestions.

![](/Images/BDB-3499-image005-233x300.jpg)

Let’s try your first recommendation by Amazon CodeWhisperer. 


**Note that this post contains examples of recommendations, but you may see different code snippets recommended by Amazon CodeWhisperer.**

Add a new cell and enter your comment to describe what you want to achieve. After you press Enter, the recommended code is shown.

If you press **Tab**, then code is chosen. If you press arrow keys, then you can select other recommendations. You can learn more in [User actions](https://docs.aws.amazon.com/codewhisperer/latest/userguide/actions-and-shortcuts.html).


## Read JSON File Example
Now let’s read a JSON file from [Amazon Simple Storage Service (Amazon S3)](https://aws.amazon.com/s3/). Enter the following code comment into a notebook cell and press **Enter**:

```
# Create a Spark DataFrame from a json file
```

CodeWhisperer will recommend a code snippet similar to the following:
```
# Create a Spark DataFrame from a json file
df = spark.read.json("s3://bucket_name/folder_name/file_name.json")
```

Now use this method to utilize the suggested code snippet:
```
df = spark.read.json("s3://awsinnovate2023-data/persons.json")
df.show()
```

The proceeding code returns the following output:

![JSON Output](/Images/JSON-Output.png)

As you can see from the result, you can quickly utilize the code snippet recommended by Amazon CodeWhisperer.

In the following sections, we provide additional examples of code recommendations. 
**Note that these are just our examples, and different code snippets may be suggested by Amazon CodeWhisperer.**

## Count Values
You can ask Amazon CodeWhisperer to recommend code to count unique values

```
#Count unique values on the birth_date column
```

Amazon CodeWhisperer will recommend a code snippet similar to the following:

```
df.select("birth_date").distinct().count()
df.show()
``````

The proceeding code returns the following output:

```
1784
```

![Count Distinct](/Images/Count-Distinct.png)



## Sort Records
You can use Amazon CodeWhisperer for sorting data and extracting records within a Spark DataFrame as well:
```
# Sort DataFrame by column given_name Descending
```

Amazon CodeWhisperer will recommend a code snippet similar to the following:
```
df.sort("given_name", ascending=False).show()
```

The proceeding code returns the following output:

![Sort](/Images/Sort.png)

## Add a column with a calculation

In extract, transform, and load (ETL) use cases, it’s common to add new columns from existing columns. When we need to add columns to our Spark DataFrame, we can articulate with a high level of detail to Amazon CodeWhisperer what type of column we need added and its respective attributes:

```
# Calculate Age in Years At Death
```

Amazon CodeWhisperer will recommend a code snippet similar to the following:

```
df.withColumn("age_at_death", datediff(col("death_date"), col("birth_date"))/365).show()
```

The proceeding code returns the following output:

![Age At Death](/Images/Age-At-Death.png)

## Generate sample datasets in a Spark DataFrame
Amazon CodeWhisperer is powerful enough to generate sample Spark DataFrames as well, which can be done like so:
```
# Generate sample Spark DataFrame of country name and country code
# First column name is country_name, and second column name is country_code
```

Amazon CodeWhisperer will recommend a code snippet similar to the following:
```
df = spark.createDataFrame([("United States", "US"), ("Canada", "CA"), ("Mexico", "MX")])
```

![Generate](/Images/Generate.png)

## Tear Down

### Delete the notebook instance
1. Click **Stop notebook**

![Stop Notebook](/Images/Stop-Notebook.png)

2. Select **Action** in the top right corner of Notebook and choose to **Delete**
![Delete Notebook](/Images/Delete-Notebook.png)

### Delete IAM Role and Policy
1. Navigate to [IAM Console](https://us-east-1.console.aws.amazon.com/iamv2/home?region=us-east-1#/roles) and delete **Glue-CodeWhisperer-GenerateRecommendations-Role** created previously
2. Delete policy **Glue-CodeWhisperer-GenerateRecommendations-Policy** created previously

