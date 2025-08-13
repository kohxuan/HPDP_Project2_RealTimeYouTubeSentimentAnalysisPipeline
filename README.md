# 📈 Real-Time YouTube Sentiment Analysis Pipeline

This project implements a complete, real-time sentiment analysis pipeline. The system scrapes YouTube comments related to a specific topic (Hari Raya), processes them in real-time using **Apache Kafka** and **Apache Spark**, and visualizes the sentiment trends on a live **Kibana** dashboard. The entire microservices architecture is containerized using **Docker Compose** for easy deployment and scalability.
<br><br>

### ✨ Features
- **Real-Time Data Ingestion**: A Python producer continuously scrapes YouTube comments and streams them into an **Apache Kafka** topic.
- **Distributed Stream Processing**: An **Apache Spark** Structured Streaming job consumes data from Kafka, performs text cleaning, and applies a sentiment analysis model in real-time.
- **Automated Sentiment Analysis**: Utilizes a pre-trained **Logistic Regression** model (trained on data labeled by a Hugging Face Transformer) to classify comments as `POSITIVE`, `NEGATIVE`, or `NEUTRAL`.
- **Real-Time Visualization**: Processed data is stored in **Elasticsearch**, and sentiment trends, word clouds, and comment distributions are visualized on a live **Kibana** dashboard.
- **Containerized Architecture**: The entire pipeline (Zookeeper, Kafka, Spark, Elasticsearch, Kibana) is orchestrated with **Docker Compose**, ensuring service isolation, portability, and simplified deployment.
- **Machine Learning Pipeline**: Includes notebooks for data preprocessing, training, and comparing sentiment models (Logistic Regression vs. Naive Bayes).
<br>

### 🛠️ Technical Overview
- **Streaming & Processing**: Apache Kafka, Apache Spark (PySpark)
- **Data Storage & Visualization**: Elasticsearch, Kibana
- **Machine Learning**: Scikit-learn (Logistic Regression, Naive Bayes, TfidfVectorizer), Hugging Face Transformers (for data labeling)
- **Orchestration**: Docker, Docker Compose
- **Data Acquisition**: Python (`youtube-comment-downloader`, `yt-dlp`)
- **Language & Data Tools**: Python, Pandas
<br>

### 📁 File Structure

- **`kafka_spark_pipeline/`**: Contains the core real-time application and its configuration.
  - `docker-compose.yml`: Defines and orchestrates all services (Kafka, Spark, Elasticsearch, etc.).
  - `producer.py`: Python script that scrapes YouTube comments and sends them to the Kafka topic.
  - `spark_stream.py`: The Spark Structured Streaming application that consumes, processes, and analyzes data.
  - `model/`: Directory containing the serialized machine learning models and the vectorizer.
    - `lr_sentiment_model.pkl`: The trained Logistic Regression model.
    - `nb_sentiment_model.pkl`: The trained Naive Bayes model.
    - `tfidf_vectorizer.pkl`: The trained TF-IDF vectorizer.
  - `README.md`: Step-by-step instructions for running the pipeline.
<br>

- **`notebooks/`**: Jupyter notebooks for model development.
  - `preprocessing.ipynb`: Notebook for cleaning and preparing the YouTube comment data.
  - `model_training.ipynb`: Notebook for training, evaluating, and saving the sentiment analysis models.
<br>

- **`data/`**: Contains the datasets used and generated.
  - `youtube_comments.csv`: Raw, scraped YouTube comments from the initial data acquisition.
  - `cleaned_youtube_comments.csv`: Cleaned and preprocessed comments ready for analysis.
<br>

- **`docs/`**: Project documentation and presentation materials.
  - `Report.pdf`: The full academic report detailing the project architecture, methodology, findings, and conclusion.
  - `PresentationSlides.pptx`: The slides for the final project presentation.
<br>

- **`dashboard/`**: Contains all Kibana visualization artifacts, including screenshots and importable configuration files.
  - `DashboardFullOverview.pdf`: A static PDF report of the full, live dashboard.
  - `exported_dashboard_visualization.ndjson`: The configuration file to import the Kibana dashboard structure and visualizations.
  - `exported_dashboard_data.ndjson`: The configuration file for the underlying data views used by the dashboard.
  - `*.png`: A collection of screenshots showing the live dashboard and its individual components (e.g., `Sentiment.png`, `Top50Comments.png`).
  - `README.md`: A dedicated guide within this folder on how to import and use the Kibana dashboard artifacts.
<br>

### 🚀 Getting Started

> **Prerequisites:**
> - [Docker](https://docs.docker.com/get-docker/) and [Docker Compose](https://docs.docker.com/compose/install/)
> - Python 3.8+ (for running the producer locally if not using a dedicated container)

1.  **Start the Docker Services**:
    Navigate to the `spark_pipeline` directory and run the following command to start all services (Kafka, Spark, Elasticsearch, etc.) in detached mode.
    ```sh
    cd spark_pipeline
    docker-compose up -d
    ```

2.  **Run the YouTube Comment Producer**:
    Open a new terminal. This script will start scraping YouTube comments and streaming them into the `youtube-comments` Kafka topic.
    ```sh
    # Navigate to the pipeline directory if you aren't already there
    python producer.py
    ```

3.  **Submit the Spark Streaming Job**:
    Open another terminal and enter the Spark container.
    ```sh
    docker exec -it spark-pipeline-spark-master-1 /bin/bash
    ```
    Inside the container, submit the Spark streaming application. This job will connect to Kafka, process comments, and write the results to Elasticsearch.
    ```sh
    /opt/bitnami/spark/bin/spark-submit \
      --packages org.apache.spark:spark-sql-kafka-0-10_2.12:3.4.2,org.elasticsearch:elasticsearch-spark-30_2.12:8.13.4 \
      /opt/bitnami/spark/work/spark_stream.py
    ```

4.  **Access the Kibana Dashboard**:
    Once data is flowing into Elasticsearch, you can visualize it in Kibana.
    - Open your browser and go to `http://localhost:5601`.
    - To import the pre-built dashboard, navigate to **Stack Management > Saved Objects**, click **Import**, and select the `dashboard/export.ndjson` file from this repository.
<br>

### 📊 Dataset Information
- **Data Source**: YouTube Comments
- **Search Query**: "Hari Raya" (to find relevant Malaysian cultural videos)
- **Videos Analyzed**: 7 popular Hari Raya-themed music videos
- **Comments Processed (Real-Time)**: 6,829 (and counting, as the pipeline runs)
<br>

### 🔍 Pipeline Workflow
1.  **Data Acquisition**: The Python `producer.py` script uses the YouTube API to find relevant videos and continuously scrapes their comments.
2.  **Data Ingestion**: Each comment is packaged as a JSON message and published to a Kafka topic named `youtube-comments`.
3.  **Stream Processing**: The `spark_stream.py` job consumes the data stream from Kafka. For each comment, it applies text cleaning functions (e.g., removing URLs, special characters).
4.  **Sentiment Prediction**: The cleaned text is vectorized using a pre-trained TF-IDF model, and its sentiment is predicted using a pre-trained Logistic Regression model.
5.  **Data Sinking**: The enriched data (original comment, cleaned comment, predicted sentiment, etc.) is written to an Elasticsearch index.
6.  **Visualization**: Kibana connects to Elasticsearch, allowing for the creation of real-time dashboards that visualize sentiment trends, top words, and other insights.
<br>

### 🔗 Assignment Source
This project was developed as part of the High Performance Data Processing course. The original assignment instructions and source repository with complete source code, data, and additional documentation can be found here:
- [*Link to Original Project Repo*](https://github.com/kohxuan/HPDP/tree/main/2425/project/p2/GroupE)
<br>

### 🤝 Team: Group E

| Name                                        | Matric No. | GitHub Profile                                         |
| :------------------------------------------ | :--------- | :----------------------------------------------------- |
| Danial Harriz Bin Mohd Asineh @Mohd Asneh | A22EC0152  | [`@danialharriz`](https://github.com/danialharriz)       |
| Chai Yu Tong                                | A22EC0145  | [`@Yutongchai`](https://github.com/Yutongchai)      |
| Koh Su Xuan                                 | A22EC0060  | [`@kohxuan`](https://github.com/kohxuan)               |
| Tiew Chuan Rong                             | A22EC0112  | [`@tiewrong`](https://github.com/tiewrong) |
<br>
