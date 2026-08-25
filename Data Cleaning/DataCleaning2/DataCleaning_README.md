# Data Cleaning Project – E-commerce Transactions

## 📌 Project Overview

This project focuses on cleaning and preprocessing a messy e-commerce transaction dataset using **Python and Pandas**.

The original dataset contained common data-quality issues such as:

- Duplicate transaction records
- Missing values
- Inconsistent date formats
- Inconsistent text capitalization
- Inconsistent category names
- Numeric values stored in different formats
- An unusually large purchase amount
- A negative purchase amount

The goal was to make the dataset more consistent and suitable for further analysis.

---

## 🛠️ Technologies Used

- Python
- Pandas
- Jupyter Notebook

---

## 🧹 Data Cleaning Steps

### 1. Load the Dataset

The dataset was loaded using Pandas:

```python
data = pd.read_csv('messy_ecommerce_data.csv')
```

### 2. Check Dataset Structure

The columns, data types, and general structure of the dataset were inspected using:

```python
data.columns
data.info()
```

This helped identify inconsistencies and missing values.

### 3. Remove Duplicate Records

The dataset contained a duplicate transaction for `TXN-105`.

Duplicate records were identified using:

```python
data.duplicated()
```

and removed using:

```python
data.drop_duplicates(inplace=True)
```

This ensured that the same transaction was not counted more than once.

### 4. Clean Purchase Amount

The `purchase_amount` column contained different formats, including values such as:

- `$150.00`
- `89.5`
- `N/A`
- `999999.00`

The column was converted to a numeric/float format after cleaning the inconsistent values.

### 5. Handle Missing Purchase Amount

`TXN-104` had a missing purchase amount.

The missing value was filled using the mean purchase amount:

```python
data['purchase_amount'].fillna(data['purchase_amount'].mean(), inplace=True)
```

This allowed the transaction to be retained instead of removing the entire record.

### 6. Handle the Extreme Purchase Amount

`TXN-107` contained a purchase amount of `999999.00`, which was treated as an unrealistic/extreme value for this dataset.

The record was removed from the cleaned dataset.

> **Note:** In a real-world project, an extreme value should ideally be investigated before deletion. It could be a genuine high-value transaction rather than an error.

### 7. Handle Negative Purchase Amount

`TXN-103` originally had a purchase amount of `-45.00`.

Initially, the value was converted to a positive value using:

```python
data['purchase_amount'] = abs(data['purchase_amount'])
```

However, this is **not necessarily the best approach**.

A negative transaction amount can represent a **refund, return, reversal, or cancellation**. Therefore, if the business rules allow negative transaction values, the original `-45.00` should be retained rather than automatically converting it to `45.00`.

For this dataset, the better approach would be to **keep `-45.00` as it is** or create a separate transaction/refund indicator if required.

### 8. Standardize Date Format

The `join_date` column contained dates in different formats, for example:

- `2023-01-15`
- `02/20/2023`
- `2023/04/01`

The dates were converted into a consistent datetime format using:

```python
data['join_date'] = pd.to_datetime(data['join_date'], format='mixed')
```

### 9. Handle Missing Records

Rows containing missing values were handled using:

```python
data.dropna(inplace=True)
```

This removed records with remaining missing values.

In this dataset, `TXN-106` had a missing `customer_name`, so the row was removed.

> **Alternative approach:** Instead of deleting the row, the missing customer name could be replaced with `"Unknown"` if retaining the transaction is important.

### 10. Standardize Text Data

Customer names, categories, and statuses were standardized:

```python
data['customer_name'] = data['customer_name'].str.title()
data['category'] = data['category'].str.lower()
data['status'] = data['status'].str.upper()
```

Examples:

| Original | Cleaned |
|---|---|
| `mike ross` | `Mike Ross` |
| `clothings` | `clothing` |
| `Completed` | `COMPLETED` |
| `cancelled` | `CANCELLED` |
| `home & kitchen` | `home & kitchen` |

The inconsistent `clothings` category was also corrected to `clothing`.

---

## 📊 Final Dataset

After cleaning, the dataset contains consistent:

- Transaction IDs
- Customer names
- Dates
- Purchase amount formatting
- Categories
- Transaction statuses

The cleaned dataset is therefore much more suitable for exploratory data analysis and machine-learning preprocessing.

---

## ⚠️ Important Data-Quality Decisions

Some cleaning operations involve assumptions and should be documented:

1. **Negative purchase amount:**  
   Initially converted from `-45.00` to `45.00`, but it can reasonably represent a refund/return. Therefore, keeping the negative value is a more appropriate option unless business rules confirm that purchase amounts must always be positive.

2. **Extreme purchase amount (`999999.00`):**  
   Removed as an apparent outlier for this exercise. In a real system, it should first be investigated.

3. **Missing customer name:**  
   The row was removed using `dropna()`. Alternatively, the missing name could be replaced with `Unknown` to preserve the transaction.

4. **Missing purchase amount:**  
   Filled using the mean purchase amount.

---

## 📁 Project Files

- `DataCleaning.ipynb` – Jupyter Notebook containing the data-cleaning process
- `messy_ecommerce_data.csv` – Original/raw dataset
- `cleaned_ecommerce_data.csv` – Cleaned dataset

---

## 🎯 Conclusion

The dataset was cleaned using Pandas by identifying and handling duplicates, missing values, inconsistent formats, invalid/extreme values, and inconsistent text data.

The process demonstrates the importance of **understanding the meaning of data before applying automatic cleaning operations**. In particular, negative values should not always be converted to positive values because they may contain useful business information such as refunds or returns.
