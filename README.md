## F1 Pit Stop Data Preprocessing Pipeline

This Apache Spark pipeline ingests, cleans, and engineers features from core Formula 1 datasets to analyze and predict post-pit stop performance dynamics.

### Core Processing Stages

* **Data Ingestion:** Loads races, lap times, pit stops, and starting grid data as distributed Spark DataFrames, applying schema inference, column standardization, and composite key joins to form a unified base dataset.


* **Missingness & Anomaly Handling:** Encodes missing schedule events into binary flags and removes impossible records, specifically targeting instances where a driver lacks prior lap data and immediately retires after pitting.


* **Dynamic Rival Tracking:** Identifies the rival driver positioned exactly one place ahead prior to the pit stop. It structurally handles edge cases, such as drivers in the last position or rivals retiring mid-chase, using conditional binary flags and safe null imputations.


* **Time Standardization:** Standardizes diverse lap and pit time strings into a uniform `HH:MM:SS.ms` format. It computes the exact physical time difference in milliseconds between the primary driver and their rival before and after the pit window.


* **Advanced Feature Engineering:**
* **Stint Lengths:** Utilizes distributed Window functions to calculate the exact number of laps completed since the last pit stop for both the driver and their targeted rival.


* **Degradation Slope:** Filters extreme outlier laps and applies a sliding-window linear regression via Spark's `applyInPandas` to calculate tire wear degradation slopes in seconds.


* **Race Context Variables:** Computes net positions gained or lost since the starting grid, counts the volume of active track traffic per lap, and calculates pit lane traffic density within a 3-lap sliding window.




* **Target Variable Isolation:** Defines `target_gap_change_ms` as the net time-gap change between the driver and their rival, dropping rows that exceed realistic millisecond thresholds.


* **Encoding & Serialization:** Performs One-Hot Encoding on categorical variables (qualifying format, circuit type, direction, and tire manufacturer), converts grid times to total computational seconds, and serializes the finalized dataset to Apache Parquet format.


* **Exploratory Data Analysis (EDA):** Leverages Pandas, Matplotlib, and Seaborn to generate statistical visualizations, including target distributions, degradation impacts, traffic density anomalies, engineered flag sparsity, and Pearson feature collinearity matrices.