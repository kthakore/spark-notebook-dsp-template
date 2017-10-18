spark-notebook-dsp-template
==============================

## Cluster Getting Started

Have docker for mac installed and run your notebooks with following config:

``` SparkConf().setMaster('spark://master:7077') ```

Make sure your jobs have `spark.executor.cores` to less than 4 and  `spark.executor.memory` to 512mb max.

An example of this is:

```
import os
config = {
    "--deploy-mode" : "client",
    "--supervise" : "",
    "--num-executors" : "2",
    "--executor-memory" : "512M",
    "--driver-memory": "512M",
    "--jars" : "/home/jovyan/work/deps/xxxx",
    "--packages" : "com.crealytics:spark-excel_2.11:0.9.5;org.apache.hadoop:hadoop-aws:2.6.0,com.amazonaws:aws-java-sdk-s3:1.11.213,com.amazonaws:aws-java-sdk-core:1.11.213,com.databricks:spark-csv_2.11:1.3.0"
    "pyspark-shell"
}

cmd = ""
for flag in config.keys():
    val = config[flag]
    cmd =  ("%s %s %s "%(cmd, flag, val))

os.environ['PYSPARK_SUBMIT_ARGS'] = cmd

import pyspark

conf = pyspark.SparkConf().setAppName('ExcelLoader').setMaster('spark://master:7077')

conf.set("spark.executor.cores","2")
conf.set("spark.shuffle.service.enabled", "false")
conf.set("spark.dynamicAllocation.enabled", "false")
conf.set("spark.io.compression.codec", "snappy")
conf.set("spark.rdd.compress", "true")
conf.set("spark.executor.memory", "512mb")


sc = pyspark.SparkContext.getOrCreate(conf)
sc.setLogLevel('INFO')


from pyspark.sql import SQLContext

sql_context = SQLContext(sc)

hadoopConf = sc._jsc.hadoopConfiguration()
hadoopConf.set("fs.s3n.awsAccessKeyId", os.environ['AWS_ACCESS_KEY'])
hadoopConf.set("fs.s3n.awsSecretAccessKey", os.environ['AWS_SECRET_KEY'])

```

Please note that you will have to add all jars to a deps folder local to the docker-compose.yml file. 

Project Organization
------------

    ├── LICENSE
    ├── Makefile           <- Makefile with commands like `make data` or `make train`
    ├── README.md          <- The top-level README for developers using this project.
    ├── data
    │   ├── external       <- Data from third party sources.
    │   ├── interim        <- Intermediate data that has been transformed.
    │   ├── processed      <- The final, canonical data sets for modeling.
    │   └── raw            <- The original, immutable data dump.
    │
    ├── deps               <- Jar files you want to load to your spark cluster
    │
    ├── docker-compose.yml <- 5 worker spark cluster 
    │
    ├── docs               <- A default Sphinx project; see sphinx-doc.org for details
    │
    ├── models             <- Trained and serialized models, model predictions, or model summaries
    │
    ├── notebooks          <- Jupyter notebooks. Naming convention is a number (for ordering),
    │                         the creator's initials, and a short `-` delimited description, e.g.
    │                         `1.0-jqp-initial-data-exploration`.
    │
    ├── references         <- Data dictionaries, manuals, and all other explanatory materials.
    │
    ├── reports            <- Generated analysis as HTML, PDF, LaTeX, etc.
    │   └── figures        <- Generated graphics and figures to be used in reporting
    │
    ├── requirements.txt   <- The requirements file for reproducing the analysis environment, e.g.
    │                         generated with `pip freeze > requirements.txt`
    │
    ├── src                <- Source code for use in this project.
    │   ├── __init__.py    <- Makes src a Python module
    │   │
    │   ├── data           <- Scripts to download or generate data
    │   │   └── make_dataset.py
    │   │
    │   ├── features       <- Scripts to turn raw data into features for modeling
    │   │   └── build_features.py
    │   │
    │   ├── models         <- Scripts to train models and then use trained models to make
    │   │   │                 predictions
    │   │   ├── predict_model.py
    │   │   └── train_model.py
    │   │
    │   └── visualization  <- Scripts to create exploratory and results oriented visualizations
    │       └── visualize.py
    │
    └── tox.ini            <- tox file with settings for running tox; see tox.testrun.org


--------

<p><small>Project based on the <a target="_blank" href="https://drivendata.github.io/cookiecutter-data-science/">cookiecutter data science project template</a>. #cookiecutterdatascience</small></p>
