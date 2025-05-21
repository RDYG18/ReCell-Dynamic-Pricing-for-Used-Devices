# Dynamic Pricing for Used Devices - ReCell 

![image](https://github.com/user-attachments/assets/68bfad6f-8fc9-4cfa-a125-6901881bb1e8)

## Table of Contents

- [Project Background](#project-background)
- [Executive Summary](#executive-summary)
- [Exploratory Data Analysis (EDA)](#exploratory-data-analysis-eda)
- [Data Preprocessing ](#data-preprocessing)
- [Model Building ](#model-building)
  - [Linear Regression Model](#linear-regression-model)
  - [Multicollinearity and Treat High P-values](#multicollinearity-and-treat-high-p-values)
  - [Final Linear Model](#final-linear-model)
  - [Threshold Optimization](#threshold-optimization)
  - [Model performance summary](#model-performance-summary)
- [Decision Tree](#decision-tree)
  - [Important features](#important-features)
  - [Pruning the tree](#pruning-the-tree)
  - [Final Feature Importances Summary](#final-feature-importances-summary)
- [Insights](#insights)
- [Business Recommendations](#business-recommendations)
- [Assumptions & Limitations](#assumptions--limitations)
- [Setup Instructions](#setup-instructions)

## Project Background

<div align="justify">

ReCell is a startup in the refurbished mobile device market, focused on offering affordable, sustainable alternatives to new electronics. By partnering with third party platforms, it provides warranty backed used smartphones and tablets while reducing e-waste.

To support its growth, ReCell seeks to optimize pricing through data driven strategies. As a Data Scientist, I worked alongside business and operations teams to build a linear regression model to help ReCell predict the resale value of used mobile devices and identify key price drivers, enabling smarter pricing decisions and supporting both competitiveness and sustainability goals.

</div>

---

## Executive Summary

<div align="justify
  
The model demonstrates solid performance, explaining approximately **80% of the variation** in resale prices, indicating a strong fit. It maintains consistent accuracy across both training and test sets, with an average prediction error of **4.8%** on normalized prices reflecting high reliability.

Key predictors such as **RAM, camera quality, 4G connectivity**, and the **original new price** have a significant positive impact on resale value, while **device age** and **uncommon operating systems** reduce it. The model also highlights that certain brands, like **Nokia and Xiaomi**, retain value better over time.

</div>

---

## Exploratory Data Analysis (EDA)

The dataset used in this project contains **8,454 rows** and **15 columns**, where each row represents a refurbished or used mobile device listed for resale. Collected in 2021, the data includes a combination of technical specifications, usage characteristics, and pricing information. 

The dataset is primarily numeric, and a few categorical fields such as brand and OS. There are no duplicate records, but a small number of **missing values** are present in columns related to hardware specifications such as main_camera_mp, selfie_camera_mp, int_memory, ram, battery, and weight.

 **Data Dictionary:**

- **brand_name**: Name of the manufacturing brand  
- **os**: Operating system running on the device  
- **screen_size**: Size of the screen in centimeters  
- **4g**: Indicates whether 4G is available (Yes/No)  
- **5g**: Indicates whether 5G is available (Yes/No)  
- **main_camera_mp**: Rear camera resolution in megapixels  
- **selfie_camera_mp**: Front camera resolution in megapixels  
- **int_memory**: Internal storage capacity (ROM) in GB  
- **ram**: RAM capacity in GB  
- **battery**: Battery size in milliamp-hours (mAh)  
- **weight**: Device weight in grams  
- **release_year**: Year the device model was launched  
- **days_used**: Number of days the device has been in use  
- **normalized_new_price**: Normalized price of a new device of the same model (in euros)  
- **normalized_used_price**: Normalized resale price of the used/refurbished device (in euros)  **target variable**

  

---

## Univariate Analysis

Both normalized used and new device prices show an approximately normal distribution, with slight skewness and some outliers.

<table>
  <tr>
    <td><img src="https://github.com/user-attachments/assets/f09c1842-9d5a-48da-be16-1934ad3303f6" width="400"/></td>
    <td><img src="https://github.com/user-attachments/assets/5805bab4-1498-4d92-873f-e760bd9e6f5c" width="400"/></td>
  </tr>
</table>

Samsung, Huawei, and LG are the most represented brands in the dataset, excluding the 'Others' category, which aggregates unregistered brands. Additionally, Android is the dominant operating system (OS) among the listed devices. 

<p>
  <img src="https://github.com/user-attachments/assets/f854fa38-dec8-4efc-a6d2-7083cd142ed9" width="550" style="display:inline-block; vertical-align:top; margin-right:20px;"/>
  <img src="https://github.com/user-attachments/assets/f392e1d8-2774-497b-ab6e-af877f7acf1f" width="400" style="display:inline-block; vertical-align:top;"/>
</p>

---

## Bivariate Analysis


<img src="https://github.com/user-attachments/assets/7814462f-e070-43c7-9745-b27e34976776" width="870"/>


**Positive Correlations** (Potential Collinearity) 

- screen_size and battery (0.81): Devices with larger screens typically have higher battery capacities, which is expected due to increased power consumption.

- screen_size and weight (0.83): Larger screens often contribute to increased device weight.

- battery and weight (0.70): Bigger batteries tend to add more weight to the device.

- normalized_used_price and normalized_new_price (0.83): The used price is heavily influenced by the original retail price.

 **Negative Correlations**  

- days_used and selfie_camera_mp (-0.69): Older devices tend to have lower resolution front cameras

- days_used and normalized_used_price (-0.51): Resale value decreases as devices age

- days_used and battery (-0.49): Older models often have smaller or more degraded batteries

- days_used and selfie_camera_mp (-0.55): Devices that have been used longer are typically older models with lower camera specs

---

**RAM**

Does higher RAM lead to higher resale value, and do brands with more RAM target different market segments through pricing?


![image](https://github.com/user-attachments/assets/7f816ea0-f9c7-4ba4-8d84-25e8b06c0e19)

Interpretation:

- **OnePlus**, **Nokia**, **Honor**, and **Huawei** offer higher RAM devices, meaning targeting mid to high end segments.

- **Infinix**, **Micromax**, and **Karbonn** consistently feature lower RAM, focusing on the budget market.

- **Samsung**, **Xiaomi**, **Oppo**, and **Vivo** show a wide range of RAM values, reflecting a diverse lineup from entry level to premium models.

- **Apple** maintains lower RAM levels, consistent with its hardware software optimization strategy.


---

**Battery**

Does higher battery capacity lead to significantly heavier devices, and how does this trade off influence design and resale value?

 **note**: For this boxplot, I focused on the relationship between battery size and weight by analyzing only smartphones with **battery capacities above 4500 mAh**, allowing for a clearer comparison of **device weight across brands** with large batteries.

![image](https://github.com/user-attachments/assets/e466c6a6-c758-482f-ab1a-b0200c378e4e)

Interpretation: 

- **Huawei**, **Lenovo**, **LG**, **Apple**, **Asus**, **Acer**, **Alcatel**, and **Samsung** show high median weights, often exceeding 400g, likely due to large batteries or heavier components.

- **Lenovo** and **LG** stand out for having wide weight variability, suggesting robust or large sized device designs.

- **Honor**, **ZTE**, and **Xiaomi** have lower median weights (around 200–250g), indicating a good balance between battery capacity and lightweight design.

- **Infinix**, **Vivo**, **Realme**, **Google**, and **Gionee** display compact weight distributions, reflecting consistency in design with minimal variation.

- **Samsung**, **Apple**, and **Huawei** present notable outliers, pointing to some heavier models, possibly due to premium materials or larger displays.

---

**Screen**

Does larger screen size lead to higher resale prices, and how does screen demand influence user preferences and market value in the second-hand device market?.  

 **note**: This barplot includes only devices with screen sizes larger than 6 inches (the standard threshold) to focus on phones with larger displays.


<img src="https://github.com/user-attachments/assets/c5e4158d-63ae-4c7e-b30c-b0719d9915ba" width="800"/>



Interpretation: 

- **Huawei** and **Samsung** lead in large screen devices, with 382 and 326 models respectively, highlighting a strong focus on display size.

- The **"Others"** category also contributes significantly, showing that the large screen trend extends beyond major brands.

- **Honor** and **Vivo** follow with over 170 devices each, suggesting that large screens are standard across their product lines.

- **Lenovo**, **LG**, and **Xiaomi** maintain solid counts above 140, indicating a balance between screen size, performance, and affordability.

- **Oppo** and **Asus**, while lower in count, still offer large-screen models, likely focusing on other components .

---

**Selfie Camera**

Does higher selfie camera quality lead to increased resale prices, and how significant is the front camera in shaping consumer value perception?

 **note**: In order to analyze which brands offer the best selfie cameras on the market, we filtered the dataset to include only devices with front cameras higher than 8 MP. 
 <img src="https://github.com/user-attachments/assets/ff35446f-f6ba-4206-af41-ce9f45e61f72" width="800"/>

 Interpretation: 

 - **Huawei** leads featuring front cameras above 8 MP, showing a strong focus on selfie camera quality.

- **Vivo**, **Samsung**, and **Oppo** follow closely, reinforcing their position in selfie focused markets.

- **Xiaomi** and **Honor** also contribute significantly,reflecting strong mid range camera offerings.

- **LG**, **Motorola**, and **ZTE** appear at the lower end, possibly indicating less emphasis on front camera resolution.

- The **"Others"** category, suggests that many lesser-known brands also compete in the high selfie camera segment.

---

**Main Camera**

How does rear camera resolution influence resale value, and to what extent is it associated with premium device positioning in a content driven market?

**note**: In order to analyze which brands offer the best main cameras on the market, we filtered the dataset to include only devices with main cameras higher than 16 MP. 

<img src="https://github.com/user-attachments/assets/d4ccdc27-812d-4326-8b4d-1cefc2842fe1" width="800"/>


Interpretation:

- **Sony** leads the category, highlighting a strong focus on high quality main cameras.

- **Motorola** and **"Others"** also are in the podium of main cameras.  

- **HTC**, **ZTE**, **Nokia**, and **Microsoft** fall in the mid range, indicating a moderate emphasis on main camera resolution.

- **Meizu**, **Samsung**, and **Asus** appear at the lower end, suggesting they either prioritize other features or offer more balanced camera specifications.

---

**4G OR 5G Network**

How does the presence of 4G or 5G connectivity affect the normalized resale price of devices in the second-hand market? 

![image](https://github.com/user-attachments/assets/4faca113-65a2-470a-bcae-3cb09ea49ccc)


Interpretation: 

- Devices with **4G** support have **higher** and more consistent resale prices, confirming 4G as a standard feature in the used market.

- **5G** enabled devices show even higher median prices, often linked to newer or premium models especially considering that, at the time this data was collected, 5G was still an emerging technology.


---

**Prices Across Years**

From 2013 to 2019, used device prices showed a steady increase, suggesting that newer models retain more resale value. Although there was a slight drop in 2020 possibly due to market saturation or rapid technological advancement the overall trend indicates that used phones are becoming more valuable, driven by better durability, higher original prices, and growing interest in sustainable tech.

  <img src="https://github.com/user-attachments/assets/e289b222-5b60-4b19-a66e-cb5cc972ba60" width="800"/>

---

## Data Preprocessing

**Missing Value Tratment** 

As mentioned earlier, we identified **491 missing values** in key columns such as main_camera_mp, selfie_camera_mp, int_memory, ram, battery, and weight. To handle them, we applied group based imputation by grouping the data by brand_name and release_year, two columns without missing values. We used the median within each group to fill the missing values, as these columns contain outliers and the median is more robust than the mean. This method provides more accurate and context aware imputations compared to using a single global value.


<table>
  <tr>
    <td><img src="https://github.com/user-attachments/assets/b70c08b1-01e4-45d4-b70c-9d08d0907699" width="200"/></td>
    <td><img src="https://github.com/user-attachments/assets/9cefb3d4-8d9c-4a1c-9733-8eec5620700d" width="200"/></td>
  </tr>
</table>



**Feature Engineering**

We are going to create a new column, years_since_release, by calculating the difference between the current year (2021) and the release_year of the product, using 2021 as a reference year to avoid altering the analysis of the recorded years. This transformation is more useful than the original release_year because the product's age is often more relevant for predictive models. After creating years_since_release, we drop the release_year column to avoid redundancy.

  <img src="https://github.com/user-attachments/assets/5c9e6046-fb24-4c2c-be78-e31e168490d3" width="500"/>

variable insights: 

The majority of devices were released between 3 and 7 years ago, with a median age of around 5 years. The distribution is balanced and free of outliers, making it suitable for modeling resale trends, depreciation, and user preferences across different product ages.


**Outlier Check** 

There are several outliers present in the dataset. However, we decided not to treat them, as they reflect valid and realistic values rather than errors or anomalies. Removing these points could distort the natural distribution and compromise the integrity of the analysis.


 <img src="https://github.com/user-attachments/assets/9dd4f5d3-e1c3-4968-97b0-50621709a846" width="500"/>

---

## Model Building

**Data preparation** 

The data was preprocessed by encoding categorical variables, adding a constant for the intercept, and splitting it into 70% training and 30% test. This ensured the dataset was clean, numeric, and ready for Linear Regression modeling and evaluation.

--- 

## Linear Regression Model

The model explains over **80% of the variance** in the target variable and maintains low prediction error on both training and test sets, indicating strong generalization and no signs of overfitting. With a **MAE** of **20%**, it predicts normalized resale prices with minimal average deviation. Finally, a **MAPE** of **4.8%** confirms the model’s ability to estimate resale prices within a tight and practical margin, making it suitable for real world pricing strategies.


<p align="center">
  <img src="https://github.com/user-attachments/assets/efd22dba-f8c2-4285-b6c8-b90a2e4a582b" alt="image" width="500"/>
</p>

---

## Multicollinearity and Treat High P-values

To improve model stability and interpretability, I first addressed multicollinearity by calculating the VIF. The analysis revealed that some features such as screen_size, brand_name_Apple, and brand_name_Others had high multicollinearity, which could distort coefficient estimates. These variables were removed, reducing VIF scores across the remaining features to acceptable levels. Additionally, I refined the model by eliminating variables with **p-values greater than 0.05**, as they were not statistically significant. This two step feature selection process enhanced the model’s robustness and ensured that only meaningful predictors were retained.


---

## Final Linear Model 

The **Linear Regression** model explains **80%** of the variation in resale prices and predicts used device values with an average error of just **4.8%**. This makes it a reliable tool for estimating second-hand value and supporting smarter pricing decisions. Also, the model shows which aspects of a phone increase its resale value and which brands retain their value.

<p align="center">
  <img src="https://github.com/user-attachments/assets/08b7172d-de91-4ce1-971e-82dbf29c26b7" alt="image" width="500"/>
</p>


## Insights

- **RAM matters**: Devices with more memory are more valuable in the second-hand market. Each additional GB of RAM increases the resale price by approximately **0.0344 units**, reflecting user preference for performance and multitasking.

- **Camera quality drives value**: Both rear and selfie cameras influence price. Each extra megapixel adds **0.0261** and **0.0182 units**, respectively, to the resale value—showing that photography remains a key purchase driver.

- **Heavier devices resell for more**: A higher weight, often linked to better build quality or more features, slightly increases resale price by **0.0017 units** per gram.

- **4G connectivity boosts value**: Devices with 4G sell for **0.0872 units** more on average, indicating continued demand for basic mobile speed standards in refurbished markets.

- **Premium devices hold value**: For every **1 unit** increase in the normalized new price, the resale value rises by **0.3005 units**, highlighting strong brand and feature retention in premium models.

- **Battery capacity has minor impact**: While relevant to user experience, its effect on resale is limited—each additional mAh only increases value by **0.00001771 units**.

- **Age reduces price**: Every additional year since release lowers resale value by **0.0119 units**, underlining the importance of model freshness.

- **Operating system influences value**: Devices running **uncommon OS** tend to depreciate more, reducing resale price by **0.1625 units**, likely due to lower compatibility and support.

- **Brand matters**:
- 
  - Brands like **Nokia**, **Xiaomi**, **Asus**, and **BlackBerry** retain value better over time, possibly due to durability or loyal user bases.  
  - **Micromax**, **Motorola**, and **ZTE** show steeper price declines, suggesting lower perceived value in secondary markets.

---

## Business Recommendations

- **Acquire high spec devices**: Focus on sourcing newer smartphones with **higher RAM**, **better camera quality**, and **4G connectivity**, as these features are linked to stronger resale value and higher buyer appeal in the second hand market.

- **Prioritize premium models**: Devices that had **higher original prices** retain value better and offer greater resale margins. Prioritizing these models can improve both revenue and inventory efficiency.

- **Leverage strong brands**: Target devices from brands with historically **strong resale performance**, such as **Nokia** and **Xiaomi**, which consistently retain value and buyer trust over time.

- **Diversify product offerings**: Consider expanding into complementary devices like **smartwatches**, which can attract tech savvy niche markets and increase average basket value.

- **Enhance customer segmentation**: Collect additional data (e.g., **age, income, preferences**) to better understand buyer behavior and optimize pricing strategies for different consumer segments.

- **Position ReCell as a sustainable brand**: Launch a targeted campaign that promotes **reuse of electronics** to reduce CO₂ emissions and e-waste. This not only aligns with global sustainability trends, but also strengthens brand identity and appeals to a growing segment of **eco conscious consumers**.

---

## Assumptions & Limitations

<div align="justify">

By applying these insights, ReCell can enhance profitability, manage inventory more efficiently, and better align product offerings with market demand, focusing on devices that truly add value rather than overstocking.As a forward looking strategy, ReCell could also replicate this model to expand into other refurbished tech categories such as smartwatches or accessories, unlocking new growth opportunities.

Additionally, I recommend launching a sustainability focused campaign to strengthen ReCell’s ESG profile by promoting device reuse and circular economy practices. This approach not only reduces environmental impact but also appeals to a growing segment of consumers who actively support responsible business models.

Combining data driven decisions with sustainability and product diversification will reinforce ReCell’s competitive position and support long term success in the evolving sustainable economy.

</div> 

--- 

## Setup Instructions
