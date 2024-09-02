# IPL Data Analysis with Apache Spark

## Overview

This project involves analyzing Indian Premier League (IPL) cricket match data using Apache Spark. The analysis includes various aspects such as player performance, match outcomes, and team statistics. The data is processed and visualized using Spark SQL and Python libraries like Matplotlib and Seaborn.

## Table of Contents

- [Introduction](#introduction)
- [Data Sources](#data-sources)
- [Setup and Installation](#setup-and-installation)
- [Data Processing](#data-processing)
- [Analysis and Visualizations](#analysis-and-visualizations)
- [Results](#results)
- [Conclusion](#conclusion)
- [Future Work](#future-work)
- [References](#references)

## Introduction

The goal of this project is to perform a comprehensive analysis of IPL match data to uncover insights about player performance, team strategies, and match outcomes. The analysis is conducted using Apache Spark for efficient data processing and Python for data visualization.

## Data Sources

The data used in this project includes:

- Ball-by-ball data
- Match data
- Player data
- Team data

These datasets are stored in CSV format and are loaded into Spark DataFrames for processing.

## Setup and Installation

To run this project, you need to have the following installed:

- Apache Spark
- Python (with libraries: PySpark, Matplotlib, Seaborn, Pandas)

### Installation Steps

1. **Clone the repository:**

    ```bash
    git clone <repository-url>
    cd IPL_Data_Analysis
    ```

2. **Install the required Python libraries:**

    ```bash
    pip install pyspark matplotlib seaborn pandas
    ```

3. **Start a Spark session:**

    ```python
    from pyspark.sql import SparkSession

    spark = SparkSession.builder.appName("IPL Data Analysis").getOrCreate()
    ```

## Data Processing

The data processing steps include:

1. **Loading the data:**

    ```python
    ball_by_ball_df = spark.read.csv("path/to/Ball_By_Ball.csv", header=True, inferSchema=True)
    match_df = spark.read.csv("path/to/Match.csv", header=True, inferSchema=True)
    player_df = spark.read.csv("path/to/Player.csv", header=True, inferSchema=True)
    team_df = spark.read.csv("path/to/Team.csv", header=True, inferSchema=True)
    ```

2. **Data Cleaning:**

    - Handling missing values
    - Normalizing player names
    - Filtering out invalid deliveries

3. **Data Transformation:**

    - Aggregating runs scored and wickets taken
    - Calculating running totals and averages
    - Categorizing players and matches

## Analysis and Visualizations

The analysis includes:

1. **Top Scoring Batsmen per Season:**

    ```python
    top_scoring_batsmen_per_season = spark.sql("""
    SELECT player_name, season_year, SUM(runs_scored) AS total_runs
    FROM ball_by_ball
    JOIN match ON ball_by_ball.match_id = match.match_id
    JOIN player_match ON match.match_id = player_match.match_id AND ball_by_ball.striker = player_match.player_id
    JOIN player ON player.player_id = player_match.player_id
    GROUP BY player_name, season_year
    ORDER BY season_year, total_runs DESC
    """)
    ```

2. **Economical Bowlers in Powerplay:**

    ```python
    economical_bowlers_powerplay = spark.sql("""
    SELECT player_name, AVG(runs_scored) AS avg_runs_per_ball, COUNT(bowler_wicket) AS total_wickets
    FROM ball_by_ball
    JOIN player_match ON ball_by_ball.match_id = player_match.match_id AND ball_by_ball.bowler = player_match.player_id
    JOIN player ON player_match.player_id = player.player_id
    WHERE over_id <= 6
    GROUP BY player_name
    HAVING COUNT(*) >= 1
    ORDER BY avg_runs_per_ball, total_wickets DESC
    """)
    ```

3. **Impact of Winning the Toss:**

    ```python
    toss_impact_individual_matches = spark.sql("""
    SELECT match_id, toss_winner, toss_name, match_winner,
           CASE WHEN toss_winner = match_winner THEN 'Won' ELSE 'Lost' END AS match_outcome
    FROM match
    WHERE toss_name IS NOT NULL
    ORDER BY match_id
    """)
    ```

## Results

The analysis reveals several insights:

- **Top Scoring Batsmen:** Identifies the highest run-scorers in each IPL season.
- **Economical Bowlers:** Highlights the bowlers with the best economy rates in powerplay overs.
- **Toss Impact:** Shows the correlation between winning the toss and winning the match.

## Conclusion

This project demonstrates the use of Apache Spark for large-scale data processing and analysis. The insights gained from the IPL data can help teams and players improve their strategies and performance.

## Future Work

Future enhancements could include:

- Incorporating more advanced machine learning models for predictive analysis.
- Expanding the dataset to include more seasons and additional features.
- Developing an interactive dashboard for real-time data visualization.

## References

- [Apache Spark Documentation](https://spark.apache.org/docs/latest/)
- [PySpark API Reference](https://spark.apache.org/docs/latest/api/python/)
- [Matplotlib Documentation](https://matplotlib.org/stable/contents.html)
- [Seaborn Documentation](https://seaborn.pydata.org/)

