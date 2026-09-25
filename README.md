# 👩‍💼 Employee-Data-Cleaning-and-Feature-Engineering-using-Python

## 📌 Project Overview

This project focuses on cleaning and transforming an employee dataset using **Python and Pandas**.

The raw dataset contained missing values, duplicate records, inconsistent text formatting, incorrect categorical values, mixed date formats, and salary values stored as text.

The project demonstrates a complete data-cleaning workflow, starting from initial data inspection through data transformation, hierarchical missing-value imputation, feature engineering, and final validation.

---

## 🎯 Project Objectives

The main objectives of this project are to:

- Inspect and understand the raw employee dataset
- Identify missing and duplicate records
- Remove unnecessary columns
- Standardize inconsistent text values
- Correct spelling errors in categorical data
- Clean and convert salary values
- Handle missing values using appropriate techniques
- Convert date and time columns into proper datetime format
- Create new features for further analysis
- Validate the final cleaned dataset

---

## 🛠️ Tools & Libraries Used

- Python
- Pandas
- NumPy
- Google Colab

---

## 🔍 Data Cleaning Process

### 1. Initial Data Inspection

The dataset was initially explored using:

- `head()` and `tail()`
- `info()`
- `columns`
- `dtypes`
- `describe()`
- `shape`
- `isnull().sum()`
- `duplicated().sum()`

This helped identify the structure, data types, missing values, and duplicate records.

### 2. Removed Unnecessary Column

The `Useless_Col` column was removed because it was not required for further analysis.

```python
df.drop("Useless_Col", axis=1, inplace=True)
```

### 3. Duplicate Handling

Duplicate records were identified and removed.

```python
df[df.duplicated(keep=False)]
df.drop_duplicates(inplace=True)
```

### 4. Name Cleaning

Employee names were standardized by:

Removing leading and trailing spaces
Converting names to title case

```python
df["Name"] = df["Name"].str.title().str.strip()
```

### 5. Missing Age Values

Missing values in the Age column were replaced using the median age.

```python
df["Age"] = df["Age"].fillna(df["Age"].median())
```

### 6. Department Cleaning

Department names were standardized using title case and whitespace removal.

A spelling error was also corrected:

```python
df["Department"] = df["Department"].str.title().str.strip()

df["Department"] = df["Department"].replace(
    "Finanace", "Finance")
```

### 7. Position Cleaning Using Regex

Regular expressions were used to remove unwanted characters from employee positions.

```python
df["Position"] = (
    df["Position"]
    .str.replace(r"[^a-zA-Z]", " ", regex=True)
    .str.title()
    .str.strip()
)
```

### 8. Salary Cleaning

Salary values containing characters such as k and commas were standardized before converting the column to numeric format.

```python
df["Salary"] = (
    df["Salary"]
    .str.replace("k", "000")
    .str.replace(",", "")
)

df["Salary"] = df["Salary"].astype(float)
```

### 9. Hierarchical Salary Imputation

Missing salary values were handled using hierarchical imputation.

Level 1: Department + Position mean

Level 2: Department mean

Level 3: Overall salary mean

```python
dept_pos_mean = (
    df.groupby(["Department", "Position"])["Salary"]
    .transform("mean")
)

dept_mean = (
    df.groupby("Department")["Salary"]
    .transform("mean")
)

overall_mean = df["Salary"].mean()

df["Salary"] = (
    df["Salary"]
    .fillna(dept_pos_mean)
    .fillna(dept_mean)
    .fillna(overall_mean)
)
```
This approach helps preserve more meaningful salary patterns compared with directly replacing all missing values with a single overall mean.

### 10. Feedback Cleaning

Feedback values were standardized and missing values were handled.

```python
df["Feedback"] = (
    df["Feedback"]
    .str.replace("?", "average")
    .str.title()
)

df["Feedback"] = df["Feedback"].fillna("Poor")
```

### 11. Car Availability Cleaning

Binary values were converted into understandable categories.

```python
df["Has_Car"] = df["Has_Car"].replace({
    "0": "No",
    "1": "Yes"
})

df["Has_Car"] = df["Has_Car"].fillna("No")
```

The column was then renamed:

```python
df.rename(
    columns={"Has_Car": "Car_Availability"},
    inplace=True)
```

### 12. Date and Time Conversion

Joining dates and last login times were converted into datetime format.

```python
df["Joining_Date"] = pd.to_datetime(
    df["Joining_Date"],
    format="mixed",
    errors="coerce",
    dayfirst=True
)

df["Last_Login_Time"] = pd.to_datetime(
    df["Last_Login_Time"],
    format="mixed",
    errors="coerce",
    dayfirst=True
)
```

Missing login times were filled using the most frequently occurring login time.

```python
df["Last_Login_Time"] = (
    df["Last_Login_Time"]
    .fillna(df["Last_Login_Time"].mode()[0])
)
```

---

## ⚙️ Feature Engineering

After cleaning the dataset, new features were created to support further analysis.

### 1. Join Year

The employee's joining year was extracted from Joining_Date.

```python
df["Join_Year"] = df["Joining_Date"].dt.year
```

### 2. Salary Range

Employees were grouped into salary categories.

```python
df["Salary_Range"] = pd.cut(
    df["Salary"],
    bins=[0, 40000, 70000, float("inf")],
    labels=["Low", "Average", "High"]
)
```

### 3. Login Hour

The login hour was extracted from Last_Login_Time.

```python
df["Login_Hour"] = df["Last_Login_Time"].dt.hour
```

### 4. Login Status

Employees were categorized based on whether they logged in before or after 12 PM.

```python
df["Login_Status"] = np.where(
    df["Login_Hour"] < 12,
    "Early Login",
    "Late Login")
```

---

## 📊 Final Data Validation

After completing the cleaning and feature-engineering process, the dataset was validated again using:

```python
df.info()
df.describe()
df.isnull().sum()
```

These checks help verify the final structure, data types, statistical distribution, and remaining missing values.

--- 

## 💡 Key Skills Demonstrated

✅ Data Cleaning

✅ Data Transformation

✅ Missing Value Handling

✅ Duplicate Handling

✅ String Manipulation

✅ Regular Expressions (Regex)

✅ Data Type Conversion

✅ Datetime Handling

✅ Hierarchical Imputation

✅ Feature Engineering

✅ Pandas

✅ NumPy

✅ Exploratory Data Analysis

---

## 🚀 Conclusion

This project demonstrates a structured approach to transforming raw employee data into a cleaner and more analysis-ready dataset.

Through this project, I practiced handling real-world data-quality issues such as missing values, duplicate records, inconsistent text, incorrect categorical values, mixed date formats, and salary formatting.

I also applied feature engineering to create additional variables such as Join Year, Salary Range, Login Hour, and Login Status, which can support further exploratory analysis and visualization.



