
# AWS Serverless Sales Data Cleaner & Visualizer

This project demonstrates a **fully serverless data pipeline** built on AWS to clean, catalog, query, and visualize sales data using:

- 🗃️ **S3** for raw and cleaned data storage
- ⚙️ **Lambda (Python)** with `pandas` for cleaning CSVs
- 🧠 **Glue Crawler** to auto-detect schema from cleaned data
- 🔍 **Athena** for SQL queries on S3
- 📊 **QuickSight** for dashboarding (optional)

---

## 🚀 Use Case
"When a raw CSV is uploaded into S3, AWS Lambda automatically cleans it using pandas and saves it into another S3 bucket. The cleaned data is queried using Athena and can be visualized with QuickSight."

---

## 📂 Architecture

![Architecture](architecture.png)

---

## 🧠 Tech Stack

| Service     | Purpose                                 |
|-------------|------------------------------------------|
| **S3**      | Store raw & cleaned data (CSV)          |
| **Lambda**  | Auto-clean CSV with `pandas`             |
| **Glue**    | Detect schema from cleaned files         |
| **Athena**  | Run SQL queries on S3 data               |
| **QuickSight** | Build dashboards (optional)          |

---

## 🔧 Lambda Code Sample (pandas-based cleaning)
```python
import boto3
import pandas as pd
import io

def lambda_handler(event, context):
    s3 = boto3.client('s3')
    source_bucket = event['Records'][0]['s3']['bucket']['name']
    key = event['Records'][0]['s3']['object']['key']

    response = s3.get_object(Bucket=source_bucket, Key=key)
    df = pd.read_csv(io.BytesIO(response['Body'].read()))
    df_clean = df.dropna()

    csv_buffer = io.StringIO()
    df_clean.to_csv(csv_buffer, index=False)

    clean_bucket = 'your-clean-sales-data'
    clean_key = key.replace('raw/', 'clean/')
    s3.put_object(Bucket=clean_bucket, Key=clean_key, Body=csv_buffer.getvalue())

    return {'statusCode': 200, 'body': 'Cleaned CSV uploaded.'}
```

---

## 📊 Athena SQL Examples
```sql
-- View first few rows
SELECT * FROM sales_cleaned_csv LIMIT 10;

-- Total sales per store
SELECT Store, SUM(Weekly_Sales) AS TotalSales
FROM sales_cleaned_csv
GROUP BY Store
ORDER BY TotalSales DESC;

-- Weekly sales trend
SELECT Date, SUM(Weekly_Sales) AS TotalSales
FROM sales_cleaned_csv
GROUP BY Date
ORDER BY Date;
```

---

## 📎 Project Files
```
sales-data-cleaner/
├── lambda/
│   └── clean_csv.py
├── sample-data/
│   └── Sales.csv
├── cleaned-data/
│   └── Sales_Cleaned.csv
├── architecture.png
├── README.md
└── athena-queries.sql
```

---

## ✅ How to Run This Project
1. Upload a CSV to `raw/` folder in S3
2. Lambda auto-cleans it and sends to `clean/`
3. Run Glue crawler
4. Query data in Athena
5. (Optional) Build visuals in QuickSight

---

## 🔗 LinkedIn Post Sample

> 🚀 Just built a fully serverless **Sales Data Cleaner & Analyzer** on AWS! 🔥
>
> 📂 Upload dirty CSVs to S3 → 💡 Lambda auto-cleans them with `pandas`
> 📊 Glue + Athena makes them queryable instantly
> 📈 And the best part? I visualized everything in **Amazon QuickSight** — all without a single EC2 or manual step.
>
> ✅ Tech Used: AWS Lambda, S3, Glue, Athena, Pandas, QuickSight
>
> Want to see how? Check out my GitHub 👉 [https://github.com/ronakrathod/aws-serverless-sales-cleaner](https://github.com/ronakrathod/aws-serverless-sales-cleaner)
>
> #AWS #DataAnalytics #Serverless #Pandas #QuickSight #Athena

---

## 🌐 GitHub Repository Guide

Create a repo called: `aws-serverless-sales-cleaner`

### Files to Upload:
- `lambda/clean_csv.py`
- `sample-data/Sales.csv`
- `cleaned-data/Sales_Cleaned.csv`
- `README.md` (this file)
- `athena-queries.sql` (SQL queries from above)
- (Optional) `architecture.png` — draw it in draw.io or Canva

### Tags for GitHub
```
Topics: aws, s3, lambda, glue, athena, quicksight, serverless, data-cleaning, pandas, analytics
```

---

Let me know if you want a license (MIT), architecture image, or GitHub Actions added!
