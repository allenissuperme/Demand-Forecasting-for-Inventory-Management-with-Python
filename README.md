
## 📌 Project Overview

This project aims to **forecast the demand** for different products over the **next month** using **time series analysis (ARIMA model)**. The output helps businesses optimize inventory levels by predicting future sales trends. We use sg nutrition's inventory and sales data to do it.

---

## ✅ Steps Followed

### **Step 1: Import Required Libraries**

We use Python libraries such as `pandas`, `numpy`, `matplotlib`, `seaborn`, and `statsmodels` for data handling, visualization, and forecasting.

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from statsmodels.tsa.arima.model import ARIMA
```

---

### **Step 2: Load Datasets**

We load the necessary datasets:

- **Sales.csv** → Historical sales data

```python
file_path = r"C:\Users\Allen\Desktop\projects\project 6-sg nutrition data base analysis\data set"

sales_df = pd.read_csv(f"{file_path}\Sales.csv")
```

---

### **Step 3: Data Preprocessing**

- Convert dates to `datetime` format.
- Rename `Order Date` to `Sale Date` for consistency.

```python
sales_df.rename(columns={"Order Date": "Sale Date"}, inplace=True)
sales_df['Sale Date'] = pd.to_datetime(sales_df['Sale Date'])
```

---

### **Step 4: Aggregate Sales Data**

- Group sales by **Sale Date and Product**.
- Calculate **total quantity sold** for each product.

```python
sales_forecast_df = sales_df.groupby(['Sale Date', 'Product'])['Quantity'].sum().reset_index()
sales_forecast_df.set_index('Sale Date', inplace=True)
```

---

### **Step 5: Apply Demand Forecasting (ARIMA)**

- Fit **ARIMA (AutoRegressive Integrated Moving Average)** model.
- Forecast **next 30 days of demand**.

```python
forecast_steps = 30
unique_products = sales_forecast_df['Product'].unique()
all_forecasts = []

for product in unique_products:
    try:
        product_sales = sales_forecast_df[sales_forecast_df['Product'] == product]['Quantity']
        model = ARIMA(product_sales, order=(5,1,0))
        model_fit = model.fit()
        forecast = model_fit.forecast(steps=forecast_steps)
        forecast_df = pd.DataFrame({'Forecasted Quantity': forecast.values, 'Product': product})
        total_forecast = forecast_df['Forecasted Quantity'].sum()
        all_forecasts.append({'Product': product, 'Forecasted Sales Next Month': total_forecast})
    except Exception as e:
        print(f"Skipping {product} due to error: {e}")
```

---

### **Step 6: Visualizing the Data**

- Plot the **historical sales trends** and **forecasted demand**.

```python
plt.figure(figsize=(12, 6))
for product in unique_products[:5]:  # Plot first 5 products for visualization
    product_sales = sales_forecast_df[sales_forecast_df['Product'] == product]['Quantity']
    plt.plot(product_sales, label=f"{product} - Historical Sales", marker="o")
    
    forecast_product = [item for item in all_forecasts if item['Product'] == product]
    if forecast_product:
        plt.plot(range(len(product_sales), len(product_sales) + forecast_steps), 
                 forecast_product[0]['Forecasted Sales Next Month'], 
                 label=f"{product} - Forecast", linestyle="dashed", marker="o")

plt.xlabel("Date")
plt.ylabel("Sales Quantity")
plt.title("Sales Forecast for Next Month")
plt.legend()
plt.show()
```

---

### **Step 7: Store and Display Results**

- Convert **forecast results** into a `DataFrame`.
- Print out all products with their **forecasted sales amount for next month**.

```python
final_forecast_df = pd.DataFrame(all_forecasts)
print(final_forecast_df)
```

---

## 📊 Expected Outcome

- **Forecasted sales for each product** over the next **30 days**.
- **Data-driven inventory management** to prevent stock shortages or overstocking.
- **Visual representation** of past and predicted sales trends.






