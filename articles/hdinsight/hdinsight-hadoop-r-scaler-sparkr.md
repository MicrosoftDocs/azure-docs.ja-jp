---
title: "Azure HDInsight で ScaleR と SparkR を組み合わせて使う | Microsoft Docs"
description: "R Server と HDInsight で ScaleR と SparkR を使用する"
services: hdinsight
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5a76f897-02e8-4437-8f2b-4fb12225854a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 5e8fb7642dca815c64b9aed8184672259d3facf8
ms.lasthandoff: 03/10/2017


---

# <a name="mixing-use-of-scaler-and-sparkr-in-hdinsight"></a>HDInsight で ScaleR と SparkR を組み合わせて使う

SparkR を使用した Spark でのデータ操作と Microsoft R Server を使用した分析とを組み合わせて使う方法について説明します。 最新の分散処理機能を活かすために、どちらのパッケージも Hadoop の Spark 実行エンジン上で動作しますが、それぞれ固有の Spark セッションが必要となることから、両者がメモリ内でデータを共有することはできません。 今後 R Server のバージョンアップでその点が改善されるまで、それぞれの Spark セッションを別々に維持し、中間ファイルを介してデータを交換するのが回避策になります。 ここで紹介する方法を使えば、この&2; つの要件は簡単に満たすことができます。

その方法を説明するために、Strata 2016 カンファレンスで Mario Inchiosa と Roni Burd が最初に紹介した例を取り上げます。「[Building a Scalable Data Science Platform with R (スケーラブルなデータ サイエンス プラットフォームを R で構築する)](http://event.on24.com/eventRegistration/console/EventConsoleNG.jsp?uimode=nextgeneration&eventid=1160288&sessionid=1&key=8F8FB9E2EB1AEE867287CD6757D5BD40&contenttype=A&eventuserid=305999&playerwidth=1000&playerheight=650&caller=previewLobby&text_language_id=en&format=fhaudio)」のウェビナーでもご覧いただけます。この例では、大手航空会社の到着遅延データセットと発着空港における気象データを SparkR を使って結合し、ScaleR ロジスティック回帰モデルの入力とすることで、フライトの到着遅延を予測します。

これから紹介するコードは、もともとは Azure HDInsight クラスターの Spark で動作する R Server 向けに書かれたものです。しかし SparkR と ScaleR を&1; つのスクリプトで組み合わせて使う概念は、オンプレミス環境にもそのまま当てはまります。 以降、R と R Server の [ScaleR](https://msdn.microsoft.com/microsoft-r/scaler-user-guide-introduction) ライブラリについて、ある程度理解している読者を対象に、[SparkR](https://spark.apache.org/docs/2.1.0/sparkr.html) の使い方も交えながら説明していきます。

## <a name="the-airline-and-weather-datasets"></a>航空データセットと気象データセット

AirOnTime08to12CSV は、一般公開されている航空データセットです。米国内の全民間飛行便を対象に 1987 年 10 月から 2012 年 12 月までの発着情報が記録されています。 この大規模なデータセットのレコード数は、およそ 1 億 5,000 万件にも上ります。 サイズは未圧縮状態で 4 GB 弱です。 このデータを[米国政府のアーカイブ](http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236)からダウンロードすることができます。また、より便利な zip ファイル (AirOnTimeCSV.zip) として入手することもできます。zip ファイルは、月ごとに分かれた 303 個の CSV ファイルを含んでおり、[Revolution Analytics のデータセット リポジトリ](http://packages.revolutionanalytics.com/datasets/AirOnTime87to12/)に置かれています。

フライト遅延に気象が及ぼす影響を把握するためには、各空港の気象データも必要です。 気象データについては、[米国海洋大気庁のリポジトリ](http://www.ncdc.noaa.gov/orders/qclcd/)から、月ごとの生データを zip ファイルとしてダウンロードできます。 この例では、その目的上、2007 年 5 月から 2012 年 12 月の気象データをダウンロードしました。この 68 か月分の各 zip ファイルに含まれる毎時のデータ ファイルを使っています。 月ごとの zip ファイルには、他にも測候所 ID (WBAN) とそれが関連付けられている空港 (CallSign)、UTC (TimeZone) を基準とする空港のタイム ゾーン オフセットという&3; つの情報のマッピング (YYYYMMstation.txt) が含まれており、航空会社の遅延データを結合する際にこれらの情報が必要となります。

## <a name="setting-up-the-spark-environment"></a>Spark 環境のセットアップ

これから気象データを準備し、航空データと結合してモデリングすることになりますが、その前にまず Spark 環境をセットアップしましょう。 最初に、必要な入力データ ディレクトリが格納されているディレクトリを指定し、Spark のコンピューティング コンテキストを作成した後、コンソールへの情報出力に必要なログ記録関数を作成します。

```
workDir        <- '~'  
myNameNode     <- 'default' 
myPort         <- 0
inputDataDir   <- 'wasb://hdfs@myAzureAcccount.blob.core.windows.net'
hdfsFS         <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# create a persistent Spark session to reduce startup times 
#   (remember to stop it later!)
 
sparkCC        <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort, persistentRun=TRUE)

# create working directories 

rxHadoopMakeDir('/user')
rxHadoopMakeDir('user/RevoShare')
rxHadoopMakeDir('user/RevoShare/remoteuser')

(dataDir <- '/share')
rxHadoopMakeDir(dataDir)
rxHadoopListFiles(dataDir) 

setwd(workDir)
getwd()

# version of rxRoc that runs in a local CC 
rxRoc <- function(...){
  rxSetComputeContext(RxLocalSeq())
  roc <- RevoScaleR::rxRoc(...)
  rxSetComputeContext(sparkCC)
  return(roc)
}

logmsg <- function(msg) { cat(format(Sys.time(), "%Y-%m-%d %H:%M:%S"),':',msg,'\n') } 
t0 <- proc.time() 

#..start 

logmsg('Start') 
(trackers <- system("mapred job -list-active-trackers", intern = TRUE))
logmsg(paste('Number of task nodes=',length(trackers)))
```

次に、R パッケージの検索パスに "Spark_Home" を追加して SparkR を使用できる状態にし、SparkR セッションを初期化します。

```
#..setup for use of SparkR  

logmsg('Initialize SparkR') 

.libPaths(c(file.path(Sys.getenv("SPARK_HOME"), "R", "lib"), .libPaths()))
library(SparkR)

sparkEnvir <- list(spark.executor.instances = '10',
                   spark.yarn.executor.memoryOverhead = '8000')

sc <- sparkR.init(
  sparkEnvir = sparkEnvir,
  sparkPackages = "com.databricks:spark-csv_2.10:1.3.0"
)

sqlContext <- sparkRSQL.init(sc)
```

## <a name="preparing-the-weather-data"></a>気象データの準備

気象データを準備するにあたって、モデリングに必要な列 ("Visibility"、"DryBulbCelsius"、"DewPointCelsius"、"RelativeHumidity"、"WindSpeed"、"Altimeter") のみを抽出し、測候所に関連付けられている空港コードを追加して、測定値をローカル時間から UTC に変換します。

まず、測候所 (WBAN) を空港コードにマッピングするためのファイルを作成します。 このファイルは、気象データに含まれているマッピング ファイルから、気象データ ファイル内の CallSign (LAX など) フィールドと航空データの Origin とを対応付けて得ることもできますが、ちょうど、WBAN を AirportID (LAX の場合は 12892) に対応付け、TimeZone を追加して CSV ファイル ("wban-to-airport-id-tz.CSV") に保存したマッピングが手元にありましたので、それを使うことにしました。以下は、そのマッピングの例です。

| AirportID | WBAN | TimeZone
|-----------|------|---------
| 10685 | 54831 | -6
| 14871 | 24232 | -8
| .. | .. | ..

次のコードは、1 時間ごとの生の気象データ ファイルを&1; つずつ読み取って必要な列を抽出し、測候所のマッピング ファイルをマージして、測定日時を UTC に調整した後、新しいバージョンのファイルとして出力しています。

```
# Look up AirportID and Timezone for WBAN (weather station ID) and adjust time

adjustTime <- function(dataList)
{
  dataset0 <- as.data.frame(dataList)
  
  dataset1 <- base::merge(dataset0, wbanToAirIDAndTZDF1, by = "WBAN")

  if(nrow(dataset1) == 0) {
    dataset1 <- data.frame(
      Visibility = numeric(0),
      DryBulbCelsius = numeric(0),
      DewPointCelsius = numeric(0),
      RelativeHumidity = numeric(0),
      WindSpeed = numeric(0),
      Altimeter = numeric(0),
      AdjustedYear = numeric(0),
      AdjustedMonth = numeric(0),
      AdjustedDay = integer(0),
      AdjustedHour = integer(0),
      AirportID = integer(0)
    )
    
    return(dataset1)
  }
  
  Year <- as.integer(substr(dataset1$Date, 1, 4))
  Month <- as.integer(substr(dataset1$Date, 5, 6))
  Day <- as.integer(substr(dataset1$Date, 7, 8))
  
  Time <- dataset1$Time
  Hour <- ceiling(Time/100)
  
  Timezone <- as.integer(dataset1$TimeZone)
  
  adjustdate = as.POSIXlt(sprintf("%4d-%02d-%02d %02d:00:00", Year, Month, Day, Hour), tz = "UTC") + Timezone * 3600

  AdjustedYear = as.POSIXlt(adjustdate)$year + 1900
  AdjustedMonth = as.POSIXlt(adjustdate)$mon + 1
  AdjustedDay   = as.POSIXlt(adjustdate)$mday
  AdjustedHour  = as.POSIXlt(adjustdate)$hour
  
  AirportID = dataset1$AirportID
  Weather = dataset1[,c("Visibility", "DryBulbCelsius", "DewPointCelsius", "RelativeHumidity", "WindSpeed", "Altimeter")]
  
  data.set = data.frame(cbind(AdjustedYear, AdjustedMonth, AdjustedDay, AdjustedHour, AirportID, Weather))
  
  return(data.set)
}

wbanToAirIDAndTZDF <- read.csv("wban-to-airport-id-tz.csv")

colInfo <- list(
  WBAN = list(type="integer"),
  Date = list(type="character"),
  Time = list(type="integer"),
  Visibility = list(type="numeric"),
  DryBulbCelsius = list(type="numeric"),
  DewPointCelsius = list(type="numeric"),
  RelativeHumidity = list(type="numeric"),
  WindSpeed = list(type="numeric"),
  Altimeter = list(type="numeric")
)

weatherDF <- RxTextData(file.path(inputDataDir, "WeatherRaw"), colInfo = colInfo)

weatherDF1 <- RxTextData(file.path(inputDataDir, "Weather"), colInfo = colInfo,
                filesystem=hdfsFS)

rxSetComputeContext("localpar")
rxDataStep(weatherDF, outFile = weatherDF1, rowsPerRead = 50000, overwrite = T,
           transformFunc = adjustTime,
           transformObjects = list(wbanToAirIDAndTZDF1 = wbanToAirIDAndTZDF))
```

## <a name="importing-the-airline-and-weather-data-to-spark-dataframes"></a>Spark DataFrames への航空データと気象データのインポート

今度は、SparkR の [read.df()](https://docs.databricks.com/spark/latest/sparkr/functions/read.df.html) 関数を使って気象データと航空データを Spark DataFrames にインポートします。 この関数は、他の多くの Spark メソッドと同様、遅延実行されることに注意してください。つまり実行キューに格納されるだけで、実際には、必要なタイミングが来るまで実行されません。

```
airPath     <- file.path(inputDataDir, "AirOnTime08to12CSV")
weatherPath <- file.path(inputDataDir, "Weather") # pre-processed weather data
rxHadoopListFiles(airPath) 
rxHadoopListFiles(weatherPath) 

# create a SparkR DataFrame for the airline data

logmsg('create a SparkR DataFrame for the airline data') 
# use inferSchema = "false" for more robust parsing
airDF <- read.df(sqlContext, airPath, source = "com.databricks.spark.csv", 
                 header = "true", inferSchema = "false")

# Create a SparkR DataFrame for the weather data

logmsg('create a SparkR DataFrame for the weather data') 
weatherDF <- read.df(sqlContext, weatherPath, source = "com.databricks.spark.csv", 
                     header = "true", inferSchema = "true")
```

## <a name="data-cleansing-and-transformation"></a>データのクレンジングと変換

次に、インポートした航空データに対してクリーンアップ処理を実行します。ここで列の名前を変更し、必要な変数のみを確保したうえで、スケジュールされた出発時刻を時 (hour) 単位に丸めて、出発前の最新の気象データとマージできる状態にします。

```
logmsg('clean the airline data') 
airDF <- rename(airDF,
                ArrDel15 = airDF$ARR_DEL15,
                Year = airDF$YEAR,
                Month = airDF$MONTH,
                DayofMonth = airDF$DAY_OF_MONTH,
                DayOfWeek = airDF$DAY_OF_WEEK,
                Carrier = airDF$UNIQUE_CARRIER,
                OriginAirportID = airDF$ORIGIN_AIRPORT_ID,
                DestAirportID = airDF$DEST_AIRPORT_ID,
                CRSDepTime = airDF$CRS_DEP_TIME,
                CRSArrTime =  airDF$CRS_ARR_TIME
)

# Select desired columns from the flight data. 
varsToKeep <- c("ArrDel15", "Year", "Month", "DayofMonth", "DayOfWeek", "Carrier", "OriginAirportID", "DestAirportID", "CRSDepTime", "CRSArrTime")
airDF <- select(airDF, varsToKeep)

# Apply schema
coltypes(airDF) <- c("character", "integer", "integer", "integer", "integer", "character", "integer", "integer", "integer", "integer")

# Round down scheduled departure time to full hour.
airDF$CRSDepTime <- floor(airDF$CRSDepTime / 100)
```

今度は、気象データに対して同様の操作を実行します。

```
# Average weather readings by hour
logmsg('clean the weather data') 
weatherDF <- agg(groupBy(weatherDF, "AdjustedYear", "AdjustedMonth", "AdjustedDay", "AdjustedHour", "AirportID"), Visibility="avg",
                  DryBulbCelsius="avg", DewPointCelsius="avg", RelativeHumidity="avg", WindSpeed="avg", Altimeter="avg"
                  )

weatherDF <- rename(weatherDF,
                    Visibility = weatherDF$'avg(Visibility)',
                    DryBulbCelsius = weatherDF$'avg(DryBulbCelsius)',
                    DewPointCelsius = weatherDF$'avg(DewPointCelsius)',
                    RelativeHumidity = weatherDF$'avg(RelativeHumidity)',
                    WindSpeed = weatherDF$'avg(WindSpeed)',
                    Altimeter = weatherDF$'avg(Altimeter)'
)
```

## <a name="joining-the-weather-and-airline-data"></a>気象データと航空データの結合

今度は、SparkR の [join()](https://docs.databricks.com/spark/latest/sparkr/functions/join.html) 関数を使い、出発空港 (AirportID) と出発時刻 (datetime) で航空データと気象データの左外部結合を実行します。 対応する気象データがなくても、外部結合を使用することですべての航空データ レコードを維持することができます。 結合後、不要な列を除去し、維持する列については名前を変更して、結合処理によって追加された受信 DataFrame プレフィックスを削除します。

```
logmsg('Join airline data with weather at Origin Airport')
joinedDF <- SparkR::join(
  airDF,
  weatherDF,
  airDF$OriginAirportID == weatherDF$AirportID &
    airDF$Year == weatherDF$AdjustedYear &
    airDF$Month == weatherDF$AdjustedMonth &
    airDF$DayofMonth == weatherDF$AdjustedDay &
    airDF$CRSDepTime == weatherDF$AdjustedHour,
  joinType = "left_outer"
)

# Remove redundant columns
vars <- names(joinedDF)
varsToDrop <- c('AdjustedYear', 'AdjustedMonth', 'AdjustedDay', 'AdjustedHour', 'AirportID')
varsToKeep <- vars[!(vars %in% varsToDrop)]
joinedDF1 <- select(joinedDF, varsToKeep)

joinedDF2 <- rename(joinedDF1,
                    VisibilityOrigin = joinedDF1$Visibility,
                    DryBulbCelsiusOrigin = joinedDF1$DryBulbCelsius,
                    DewPointCelsiusOrigin = joinedDF1$DewPointCelsius,
                    RelativeHumidityOrigin = joinedDF1$RelativeHumidity,
                    WindSpeedOrigin = joinedDF1$WindSpeed,
                    AltimeterOrigin = joinedDF1$Altimeter
)
```

同様に、到着空港 (AirportID) と到着時刻 (datetime) に基づいて、気象データと航空データを結合します。

```
logmsg('Join airline data with weather at Destination Airport')
joinedDF3 <- SparkR::join(
  joinedDF2,
  weatherDF,
  airDF$DestAirportID == weatherDF$AirportID &
    airDF$Year == weatherDF$AdjustedYear &
    airDF$Month == weatherDF$AdjustedMonth &
    airDF$DayofMonth == weatherDF$AdjustedDay &
    airDF$CRSDepTime == weatherDF$AdjustedHour,
  joinType = "left_outer"
)

# Remove redundant columns
vars <- names(joinedDF3)
varsToDrop <- c('AdjustedYear', 'AdjustedMonth', 'AdjustedDay', 'AdjustedHour', 'AirportID')
varsToKeep <- vars[!(vars %in% varsToDrop)]
joinedDF4 <- select(joinedDF3, varsToKeep)

joinedDF5 <- rename(joinedDF4,
                    VisibilityDest = joinedDF4$Visibility,
                    DryBulbCelsiusDest = joinedDF4$DryBulbCelsius,
                    DewPointCelsiusDest = joinedDF4$DewPointCelsius,
                    RelativeHumidityDest = joinedDF4$RelativeHumidity,
                    WindSpeedDest = joinedDF4$WindSpeed,
                    AltimeterDest = joinedDF4$Altimeter
                    )
```

## <a name="save-results-to-csv-for-exchange-with-scaler"></a>結果を CSV に保存して ScaleR とデータを交換できるようにする

作業に必要なデータ結合が完了したら、SparkR の作業の仕上げを行います。 最終的に得られた Spark DataFrame "joinedDF5" から、ScaleR への入力データとなる CSV にデータを保存し、SparkR セッションを終了します。 ScaleR の処理で適度な並列度を確保するために、最終的に得られた CSV を 80 個の独立したパーティションに保存するよう SparkR に明示的に命令を与えています。

```
logmsg('output the joined data from Spark to CSV') 
joinedDF5 <- repartition(joinedDF5, 80) # write.df below will produce this many CSVs

# write result to directory of CSVs
write.df(joinedDF5, file.path(dataDir, "joined5Csv"), "com.databricks.spark.csv", "overwrite", header = "true")

# We can shut down the SparkR Spark context now
sparkR.stop()

# remove non-data files
rxHadoopRemove(file.path(dataDir, "joined5Csv/_SUCCESS"))
```

## <a name="import-to-xdf-for-use-by-scaler"></a>ScaleR で使用するための XDF へのインポート

モデリングには、航空データと気象データを結合した CSV ファイルを ScaleR テキスト データ ソースを介してそのまま使うこともできますが、データセットに対していくつもの操作を実行するときは、XDF の方が効率的であるため、ここでは XDF にインポートして使うことにします。

```
logmsg('Import the CSV to compressed, binary XDF format') 

# set the Spark compute context for R Server 
rxSetComputeContext(sparkCC)
rxGetComputeContext()

colInfo <- list(
  ArrDel15 = list(type="numeric"),
  Year = list(type="factor"),
  Month = list(type="factor"),
  DayofMonth = list(type="factor"),
  DayOfWeek = list(type="factor"),
  Carrier = list(type="factor"),
  OriginAirportID = list(type="factor"),
  DestAirportID = list(type="factor"),
  RelativeHumidityOrigin = list(type="numeric"),
  AltimeterOrigin = list(type="numeric"),
  DryBulbCelsiusOrigin = list(type="numeric"),
  WindSpeedOrigin = list(type="numeric"),
  VisibilityOrigin = list(type="numeric"),
  DewPointCelsiusOrigin = list(type="numeric"),
  RelativeHumidityDest = list(type="numeric"),
  AltimeterDest = list(type="numeric"),
  DryBulbCelsiusDest = list(type="numeric"),
  WindSpeedDest = list(type="numeric"),
  VisibilityDest = list(type="numeric"),
  DewPointCelsiusDest = list(type="numeric")
)

joinedDF5Txt <- RxTextData(file.path(dataDir, "joined5Csv"),
                           colInfo = colInfo, fileSystem = hdfsFS)
rxGetInfo(joinedDF5Txt) 

destData <- RxXdfData(file.path(dataDir, "joined5XDF"), fileSystem = hdfsFS)

rxImport(inData = joinedDF5Txt, destData, overwrite = TRUE)

rxGetInfo(destData, getVarInfo = T)

# File name: /user/RevoShare/dev/delayDataLarge/joined5XDF 
# Number of composite data files: 80 
# Number of observations: 148619655 
# Number of variables: 22 
# Number of blocks: 320 
# Compression type: zlib 
# Variable information: 
#   Var 1: ArrDel15, Type: numeric, Low/High: (0.0000, 1.0000)
# Var 2: Year
# 26 factor levels: 1987 1988 1989 1990 1991 ... 2008 2009 2010 2011 2012
# Var 3: Month
# 12 factor levels: 10 11 12 1 2 ... 5 6 7 8 9
# Var 4: DayofMonth
# 31 factor levels: 1 3 4 5 7 ... 29 30 2 18 31
# Var 5: DayOfWeek
# 7 factor levels: 4 6 7 1 3 2 5
# Var 6: Carrier
# 30 factor levels: PI UA US AA DL ... HA F9 YV 9E VX
# Var 7: OriginAirportID
# 374 factor levels: 15249 12264 11042 15412 13930 ... 13341 10559 14314 11711 10558
# Var 8: DestAirportID
# 378 factor levels: 13303 14492 10721 11057 13198 ... 14802 11711 11931 12899 10559
# Var 9: CRSDepTime, Type: integer, Low/High: (0, 24)
# Var 10: CRSArrTime, Type: integer, Low/High: (0, 2400)
# Var 11: RelativeHumidityOrigin, Type: numeric, Low/High: (0.0000, 100.0000)
# Var 12: AltimeterOrigin, Type: numeric, Low/High: (28.1700, 31.1600)
# Var 13: DryBulbCelsiusOrigin, Type: numeric, Low/High: (-46.1000, 47.8000)
# Var 14: WindSpeedOrigin, Type: numeric, Low/High: (0.0000, 81.0000)
# Var 15: VisibilityOrigin, Type: numeric, Low/High: (0.0000, 90.0000)
# Var 16: DewPointCelsiusOrigin, Type: numeric, Low/High: (-41.7000, 29.0000)
# Var 17: RelativeHumidityDest, Type: numeric, Low/High: (0.0000, 100.0000)
# Var 18: AltimeterDest, Type: numeric, Low/High: (28.1700, 31.1600)
# Var 19: DryBulbCelsiusDest, Type: numeric, Low/High: (-46.1000, 53.9000)
# Var 20: WindSpeedDest, Type: numeric, Low/High: (0.0000, 136.0000)
# Var 21: VisibilityDest, Type: numeric, Low/High: (0.0000, 88.0000)
# Var 22: DewPointCelsiusDest, Type: numeric, Low/High: (-43.0000, 29.0000)

finalData <- RxXdfData(file.path(dataDir, "joined5XDF"), fileSystem = hdfsFS)

```

## <a name="splitting-data-for-training-and-test"></a>トレーニングとテストのためのデータ分割

rxDataStep を使って 2012 年のデータをテスト用に切り出し、それ以外のデータをトレーニング用に確保します。

```
# split out the training data

logmsg('split out training data as all data except year 2012')
trainDS <- RxXdfData( file.path(dataDir, "finalDataTrain" ),fileSystem = hdfsFS)

rxDataStep( inData = finalData, outFile = trainDS,
            rowSelection = ( Year != 2012 ), overwrite = T )

# split out the testing data

logmsg('split out the test data for year 2012') 
testDS <- RxXdfData( file.path(dataDir, "finalDataTest" ), fileSystem = hdfsFS)

rxDataStep( inData = finalData, outFile = testDS,
            rowSelection = ( Year == 2012 ), overwrite = T )

rxGetInfo(trainDS)
rxGetInfo(testDS)
```

## <a name="train-and-test-a-logistic-regression-model"></a>ロジスティック回帰モデルのトレーニングとテスト

以上でモデルを構築する準備が整いました。 気象データが到着時刻の遅延に及ぼす影響を調べるために、相対日付や発着空港、発着空港における気象などの条件が、15 分を超える到着遅延に関係しているかどうかを、ScaleR のロジスティック回帰ルーチンを使ってモデル化します。

```
logmsg('train a logistic regression model for Arrival Delay > 15 minutes') 
formula <- as.formula(ArrDel15 ~ Year + Month + DayofMonth + DayOfWeek + Carrier +
                     OriginAirportID + DestAirportID + CRSDepTime + CRSArrTime + 
                     RelativeHumidityOrigin + AltimeterOrigin + DryBulbCelsiusOrigin +
                     WindSpeedOrigin + VisibilityOrigin + DewPointCelsiusOrigin + 
                     RelativeHumidityDest + AltimeterDest + DryBulbCelsiusDest +
                     WindSpeedDest + VisibilityDest + DewPointCelsiusDest
                   )

# Use the scalable rxLogit() function but set max iterations to 3 for the purposes of 
# this exercise 

logitModel <- rxLogit(formula, data = trainDS, maxIterations = 3)

base::summary(logitModel)
```

それでは実際に見てみましょう。テスト データでいくつかの予測を行い、ROC と AUC に注目します。

```
# Predict over test data (Logistic Regression).

logmsg('predict over the test data') 
logitPredict <- RxXdfData(file.path(dataDir, "logitPredict"), fileSystem = hdfsFS)

# Use the scalable rxPredict() function

rxPredict(logitModel, data = testDS, outData = logitPredict,
          extraVarsToWrite = c("ArrDel15"), 
          type = 'response', overwrite = TRUE)

# Calculate ROC and Area Under the Curve (AUC).

logmsg('calculate the roc and auc') 
logitRoc <- rxRoc("ArrDel15", "ArrDel15_Pred", logitPredict)
logitAuc <- rxAuc(logitRoc)
head(logitAuc)
logitAuc

plot(logitRoc)
```

## <a name="scoring-elsewhere"></a>外部でのスコア付け

データのスコア付けのモデルは、別のプラットフォームに移して使うこともできます。その場合は、モデルを RDS ファイルに保存し、それを移行先のスコア付け環境 (SQL Server R Services など) に転送してインポートします。 このとき、スコア付けの対象となるデータのファクター レベルは、モデルが構築された環境と確実に合わせることが重要です。 この要件は、モデリング データに関連付けられている列情報を ScaleR の rxCreateColInfo() 関数で抽出して保存し、その列情報を予測の入力データ ソースに適用することで満たすことができます。 以下のコードでは、テスト データセットからいくつかの行を保存し、そのサンプルから列情報を抽出して予測スクリプトに使用しています。

```
# save the model and a sample of the test dataset 

logmsg('save serialized version of the model and a sample of the test data')
rxSetComputeContext('localpar') 
saveRDS(logitModel, file = "logitModel.rds")
testDF <- head(testDS, 1000)  
saveRDS(testDF    , file = "testDF.rds"    )
list.files()

rxHadoopListFiles(file.path(inputDataDir,''))
rxHadoopListFiles(dataDir)

# stop the spark engine 
rxStopEngine(sparkCC) 

logmsg('Done.')
elapsed <- (proc.time() - t0)[3]
logmsg(paste('Elapsed time=',sprintf('%6.2f',elapsed),'(sec)\n\n'))
```

## <a name="summary"></a>まとめ

これで終了です。 この記事では、Hadoop Spark で SparkR を使ったデータ操作と ScaleR を使ったモデル開発を組み合わせて使う方法を紹介しました。ただし、ひとつ注意点があります。Spark セッションが互いにかち合わないよう、一度に&1; セッションずつ実行して、データを CSV ファイルを介して交換する必要があります。 単純な方法ではありますが、このプロセスによって、新しい R Server が今後リリースされ、SparkR と ScaleR とで Spark セッション (ひいては Spark DataFrames) を共有できるようになったときの移行作業がはるかに楽になります。

## <a name="next-steps-and-more-information"></a>次のステップと詳細情報

- Spark での R Server の使用について詳しくは、[MSDN のガイド](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started)をご覧ください。

- R Server の一般情報については、[R の基礎](https://msdn.microsoft.com/microsoft-r/microsoft-r-get-started-node)に関する記事をご覧ください。

- その他、[Azure HDInsight で R Server を利用する方法](hdinsight-hadoop-r-server-get-started.md)と [Azure HDInsight における R Server の概要](hdinsight-hadoop-r-server-overview.md)についての記事も参考になります。

SparkR の使い方について詳しくは、次のドキュメントをご覧ください。

- [Apache SparkR のドキュメント](https://spark.apache.org/docs/2.1.0/sparkr.html)

- [SparkR の概要](https://docs.databricks.com/spark/latest/sparkr/overview.html) (Databricks のサイト)

