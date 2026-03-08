# Data Science & Machine Learning Learning Guide: Hotel Booking Cancellation Prediction

This guide is designed to help you understand the technical decisions, models, and strategies used in the "INN Hotels Group" project. It transitions from basic concepts to professional optimization techniques.

---

## 1. Understanding the Data (Exploratory Data Analysis - EDA)
EDA is the foundation of any ML project. We performed three levels of analysis:

### 📊 Univariate Analysis
Focuses on individual variables to understand their distribution.
*   **Example:** We found that **October** is the busiest month.
*   **Technical Insight:** Knowing the distribution helps us identify skewness or the need for specific scaling.

### 📈 Bivariate Analysis
Explores the relationship between two variables.
*   **Example:** We looked at how **Lead Time** affects **Booking Status**.
*   **Technical Insight:** Longer lead times often correlate with higher cancellation rates due to increased uncertainty over time.

### 🕸️ Multivariate Analysis
Examines the interaction between three or more variables.
*   **Example:** Analyzing **Market Segment, Month, and Price** simultaneously to see how they jointly influence cancellations.

---

## 2. Preprocessing: Preparing the "Fuel"
Algorithms are only as good as the data you feed them.

### 🔠 Encoding
Most ML models only understand numbers.
*   **Label Encoding:** Converting 'Canceled' to `1` and 'Not Canceled' to `0`.
*   **One-Hot Encoding:** Converting categorical variables like `market_segment_type` into multiple binary columns (e.g., `segment_Online`, `segment_Offline`). This prevents the model from assuming a mathematical order (like Online > Offline) where none exists.

### ⚖️ Feature Scaling
*   **The Problem:** SVM and KNN are distance-based. If `Price` is 200 and `Children` is 1, the model thinks the price is 200x more important.
*   **The Solution:** We used `StandardScaler` to normalize all features to have a mean of 0 and a variance of 1.

---

## 3. Deep Dive into Models

### 🗺️ K-Nearest Neighbors (KNN)
*   **Concept:** "Birds of a feather flock together." It classifies a new booking based on how similar it is to historical ones.
*   **Pro Tip:** It performs well but can be slow if the dataset is massive because it has to calculate distances to every single point.

### 🎲 Naive Bayes
*   **Concept:** Based on Bayes' Theorem, it calculates the probability of a class based on prior knowledge.
*   **Pro Tip:** It's incredibly fast and works well for high-dimensional data, though it assumes all features are independent (which is rarely true in the real world).

### 🛡️ Support Vector Machine (SVM)
*   **Concept:** It tries to find the optimal "hyperplane" (a boundary) that separates the classes with the maximum margin.
*   **The Bottleneck:** SVM complexity grows cubically with the number of samples ($O(n^3)$). This is why 36,000 rows caused a 46-minute delay.

---

## 4. Professional Optimization: Sampling & Timing
When SVM slows down, professionals don't just wait—they optimize.

### 🧪 Stratified Sampling
We took a **10% sample** (3,600 rows) for hyperparameter tuning.
*   **Why Stratified?** It ensures the ratio of 'Canceled' vs 'Not Canceled' in the sample matches the full dataset, keeping the analysis fair.

### ⚡ Tuning vs. Training
1.  **Tuning:** We use `RandomizedSearchCV` on the **10% sample** to quickly find the best parameters (like `C` or `kernel`).
2.  **Final Training:** Once the best parameters are found, we train the model on the **100% full dataset**. This gives us the best of both worlds: speed during search and maximum accuracy for the final model.

---

## 5. Evaluation Metrics: Beyond Accuracy
Accuracy can be misleading if the data is imbalanced.

*   **Recall:** How many of the *actual* cancellations did we catch? (Crucial for minimizing lost hotel revenue).
*   **Precision:** When we predicted a cancellation, how often were we right? (Crucial to avoid overbooking mistakes).
*   **F1-Score:** The harmonic mean of Precision and Recall. This was our primary target for a balanced model.

---

> **Learner's Note:** A great data scientist handles technical bottlenecks (like SVM slowness) with strategic solutions (like Sampling). You've now mastered not just the code, but the *strategy* of Machine Learning!
