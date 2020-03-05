# 303 SparkSQL

Module 1, Big Data course (81932), University of Bologna.

## 303-1 Create DataFrames

Load a JSON file (Movies)

```shell
# Spark 1
val sqlContext = new org.apache.spark.sql.SQLContext(sc)
val dfMovies = sqlContext.jsonFile("/bigdata/dataset/movies/movies.json")
# Spark 2
val dfMovies = spark.read.json("/bigdata/dataset/movies/movies.json")
val dfMovies = spark.read.format("json").load("/bigdata/dataset/movies/movies.json")
```

Load a CSV without a defined schema (Population)

```shell
import org.apache.spark.sql.Row
import org.apache.spark.sql.types.{StructType,StructField,StringType}
val population = sc.textFile("/bigdata/dataset/population/zipcode_population.csv")
val schemaString = "zipcode total_population avg_age male female"
val schema = StructType(schemaString.split(" ").map(fieldName ⇒ StructField(fieldName, StringType, true)))
val rowRDD = population.map(_.split(";")).map(e ⇒ Row(e(0), e(1), e(2), e(3), e(4)))
val dfPeople = sqlContext.createDataFrame(rowRDD, schema)
```

Load a TXT with the schema in the first row (Real estate transactions)

```shell
# Spark 1
val transaction_RDD = sc.textFile("/bigdata/dataset/real_estate/real_estate_transactions.txt")
val schema_array = transaction_RDD.take(1)
val schema_string = schema_array(0)
val schema = StructType(schema_string.split(';').map(fieldName ⇒ StructField(fieldName, StringType, true)))
val rowRDD = transaction_RDD.map(_.split(";")).map(e ⇒ Row(e(0), e(1), e(2), e(3), e(4)))
val dfTransactionsTmp = sqlContext.createDataFrame(rowRDD, schema)
val dfTransactions = dfTransactionsTmp.where("street <> 'street'")
# Spark 2
val dfTransactions = sqlContext.read.format("csv").option("header", "true").option("delimiter",";").load("/bigdata/dataset/real_estate/real_estate_transactions.txt")
```

Load a Parquet file (User data)

```shell
# Spark 1
val dfUserData = sqlContext.read.load("/bigdata/dataset/userdata/userdata.parquet")
# Spark 2
spark.read.format("json").load("/bigdata/dataset/userdata/userdata.parquet")
```

Load a Hive table (Geography)

```shell
# Spark 1
val hiveContext = new org.apache.spark.sql.hive.HiveContext(sc)
hiveContext.sql("use default")
val dfGeo = hiveContext.sql("select * from geo")
# Spark 2
spark.sql("use default")
val dfGeo = spark.sql("select * from geo")
```

## 303-2 Write DataFrames

Write to JSON on HDFS

```shell
peopleDF.write.mode("append").json("people.json")
```

Write to table on Hive (create a new database, select it, and write the table)

```shell
# Replace [username] with your username; the database name should be something like user_egallinucci
hiveContext.sql("create database user_[username]")
hiveContext.sql("use user_[username]")
# Spark 1: reload parquet_df with the hiveContext object
val parquet_df = hiveContext.read.load("/bigdata/dataset/userdata/userdata.parquet")
# Spark 1/2
parquet_df.write.saveAsTable("parquet_table")
parquet_df.write.mode("overwrite").saveAsTable("parquet_table")
parquet_df.write.format("parquet").mode("overwrite").saveAsTable("parquet_table")
```

Write to Parquet file on HDFS

```shell
df_movies.write.parquet("movies.parquet")
```

## 303-3 Basic SQL Operations

## 303-4 RDD Operations

## 303-5 Execution plan evalutaion

## 303-6 Performance evaluation

