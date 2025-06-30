**Apache** **Airflow** **Documentation**

**Redback** **Data** **Warehouse** **Project** **3**

**1.** **Purpose**

This documentation explains how Apache Airflow was used to automate the
data pipeline for structured CSV files. The pipeline performs Extract,
Transform, and Load (ETL) tasks to clean incoming data files
automatically and save the cleaned output.

**2.** **Why** **Apache** **Airflow**

> • Automates repetitive ETL processes.
>
> • Makes the pipeline consistent, scheduled, and easy to update.
>
> • Ideal for cleaning incoming athlete telemetry data files (e.g.,
> heart rate, speed).
>
> • Allow monitoring and error tracking.

That’s where Apache Airflow fits.

Airflow makes it easier to build and manage ETL workflows. It lets us
design how data moves through each stage, from raw to clean, with clear
task tracking and alerts when something breaks.

**We** **followed** **a** **typical** **ETL** **structure:**

> • **Extract** the data (from CSV or Excel).
>
> • **Transform** by cleaning it: removing nulls, renaming columns,
> normalizing units.
>
> • **Load** the result into a new file ready for analysis or further
> processing.

**Tools** **Used**

> • **Docker**: Airflow runs on containers. Docker simplifies the
> environment setup.
>
> • **PowerShell**: We used PowerShell to run the Docker commands.
>
> • **Airflow** **UI** **(localhost:8080)**: To visually monitor,
> trigger, or debug tasks.

**Why** **Airflow** **Matters** **for** **This** **Project**

> • Our data comes multiple times a day, sometimes in different formats.
>
> • Cleaning the data manually each time wastes time and increases
> error.
>
> • Airflow lets us define the cleaning rules once, then apply them
> automatically.

<img src="./Docker.png"
style="width:6.26805in;height:2.22222in" />

> • When something breaks, the UI shows exactly where and why.

**Specific** **Gains:**

> • **Consistency**: Every data batch gets the same cleaning steps.
>
> • **Error** **visibility**: We caught issues like missing Excel
> libraries using the log system.
>
> • **Eficiency**: We can scale up with more DAGs as new data sources
> are added.

**3.** **Environment** **Setup** **(Docker-Based)**

**Steps:**

> • **Docker** **Installed**: Docker Desktop on Windows.
>
> <img src="./DockerCompose.png" style="width:6.26805in;height:3in" />•
> **Docker** **Compose**: Used to spin up Airflow services (webserver,
> scheduler, PostgreSQL).

<img src="./AirflowUI.png"
style="width:6.26805in;height:2.62222in" /><img src="./VolumeMapped.png"
style="width:6.26805in;height:2.73819in" />

> • **Airflow** **UI**: Accessed at
> [<u>http://localhost:8080</u>.](http://localhost:8080/)
>
> • **Volumes** **Mapped**: DAGs and CSV data mounted to
> /opt/airflow/dags/.

**Issue** **Fixed:**

> • File saving failed due to Windows folder permission. Solved by
> running Docker as administrator and adjusting folder rights.

**4.** **ETL** **Pipeline** **Design**

**Extract:**

> • Loads CSV files placed in a shared folder.

<img src="./LoadCSVFile.png"
style="width:6.26708in;height:3.42361in" />

> • Handles one or multiple CSVs.

**Transform:**

> • Normalizes timestamps.
>
> • Removes duplicates.
>
> • Fills missing values.
>
> • Fixes column data types.
>
> • Standardizes column names.
>
> • (Planned) Outlier removal.

**Load:**

> • Saves cleaned data as a new CSV (clean_output.csv) in the mounted
> volume.

**5.** **DAG** **Implementation**

**DAG** **Name:**

project3_dag.py

**Tasks:**

> 1\. **start** – DummyOperator to mark the start.
>
> 2\. **read_csv** – Reads raw CSV with pandas.read_csv().

<img src="./readCSV.png"
style="width:6.26736in;height:3.4318in" />

> 3\. **clean_data** – Applies cleaning logic (timestamp fix, nulls,
> renaming).
>
> 4\. **save_data** – Saves cleaned DataFrame to CSV.

**Task** **Flow:**

start \>\> read_csv \>\> clean_data \>\> save_data

**6.** **Scheduling**

> • Set schedule_interval=None for manual testing.
>
> • Can be scheduled daily/weekly when needed.
>
> • Future: Use sensors for real-time file detection.

**7.** **Testing**

> • CSV file with 691 rows tested.
>
> • Logs verified through Airflow UI.
>
> • Output CSV matched expected format and structure.
>
> • Confirmed removal of duplicates and handling of missing values.

<img src="./summeryStatistic.png"
style="width:6.26792in;height:3.23194in" />

**8.** **Challenges** **&** **Fixes**

||
||
||
||
||
||
||

**9.** **Outcome** **and** **Comparisons**

> • Airflow DAG reads, cleans, and outputs CSV files.
>
> • Output is clean, reliable, and ready for downstream use.
>
> • Fully automated and easily extendable.

Before Garmin_run_data.csv - Unstructured, missing values, inconsistent
formats.

<img src="./GarminRawData.png"
style="width:6.26805in;height:3.51805in" /><img src="./AggregatedCSV.png"
style="width:6.26805in;height:4.39861in" />

After Garmin_run_data_aggregated.csv - Cleaned, standardized, and
summarized

**DAG** **Task** **Flow**

<img src="./AggregatUIAIrflow.png"
style="width:6.26736in;height:3.39972in" /><img src="./StatisticCSV.png"
style="width:6.26653in;height:1.62361in" />

**Data** **Statistics**

> • Before: Raw, incomplete data statistics.
>
> After Comprehensive summary with average, max, min values.
>
> <img src="./statisticUI.png"
> style="width:6.26805in;height:3.24792in" />**DAG** **Task** **Flow**
>
> <img src="./statiticAfter.png"
> style="width:6.26805in;height:4.35555in" />Statistics After in
> statistics.csv file
