import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# 1. LOAD THE DATA
# Replace 'your_dataset.csv' with the actual path to your project file
try:
    df = pd.read_csv('your_dataset.csv')
    print("--- Data successfully loaded ---")
    print(df.info())
except FileNotFoundError:
    print("Error: Please provide a valid path to your dataset file.")
    # Creating dummy data so the script runs cleanly as a test
    import numpy as np
    data = {
        'ID':,
        'Age': [18, 19, 19, np.nan, 22, 115], # 115 is an outlier
        'Score': [85, 90, 90, 75, np.nan, 95] # Contains missing value
    }
    df = pd.DataFrame(data)
    print("\nUsing a sample dataset for demonstration:")
    print(df)

# 2. DATA CLEANING
print("\n--- Starting Data Cleaning ---")

# Handle Duplicates
duplicate_count = df.duplicated().sum()
df = df.drop_duplicates()
print(f"Removed {duplicate_count} duplicate rows.")

# Handle Missing Values
# Option: Fill numeric missing values with the column median
for col in df.select_dtypes(include=[np.number]).columns:
    if df[col].isnull().any():
        median_val = df[col].median()
        df[col] = df[col].fillna(median_val)
        print(f"Filled missing values in '{col}' with median: {median_val}")

# Handle Outliers (Using Interquartile Range / IQR Method for 'Age')
if 'Age' in df.columns:
    Q1 = df['Age'].quantile(0.25)
    Q3 = df['Age'].quantile(0.75)
    IQR = Q3 - Q1
    lower_bound = Q1 - 1.5 * IQR
    upper_bound = Q3 + 1.5 * IQR
    
    # Cap outliers to the upper and lower bounds
    df['Age'] = np.where(df['Age'] > upper_bound, upper_bound, df['Age'])
    df['Age'] = np.where(df['Age'] < lower_bound, lower_bound, df['Age'])
    print("Outliers handled by capping extreme values.")

print("\nCleaned Data Preview:")
print(df)

# 3. DATA VISUALIZATION & STORYTELLING
print("\n--- Generating Visual Reports ---")

# Set up a clean style for graphics
sns.set_theme(style="whitegrid")
fig, axes = plt.subplots(1, 2, figsize=(12, 5))

# Visualization 1: Distribution Plot
if 'Age' in df.columns:
    sns.histplot(df['Age'], kde=True, ax=axes[0], color='skyblue')
    axes[0].set_title('Age Distribution (Cleaned)')
    axes[0].set_xlabel('Age')

# Visualization 2: Relationship/Trend Plot
if 'Age' in df.columns and 'Score' in df.columns:
    sns.scatterplot(x='Age', y='Score', data=df, ax=axes[1], color='coral', s=100)
    axes[1].set_title('Age vs Performance Score')
    axes[1].set_xlabel('Age')
    axes[1].set_ylabel('Score')

plt.tight_layout()

# Save the visual report to a file
plt.savefig('visual_report.png')
print("Visual report saved successfully as 'visual_report.png'!")
plt.show()
