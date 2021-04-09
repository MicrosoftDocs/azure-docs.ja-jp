---
title: Azure SQL Edge で T-SQL ストリーミング ジョブを作成する
description: Azure SQL Edge で Stream Analytics ジョブを作成する方法について説明します。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/27/2020
ms.openlocfilehash: 97189fd7a232c2467981b23dc20da51ebef08252
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97656344"
---
# <a name="create-a-data-streaming-job-in-azure-sql-edge"></a>Azure SQL Edge でデータ ストリーミング ジョブを作成する 

この記事では、Azure SQL Edge で T-SQL ストリーミング ジョブを作成する方法を説明します。 外部ストリームの入力オブジェクトと出力オブジェクトを作成してから、ストリーミング ジョブの作成の一部としてストリーミング ジョブ クエリを定義します。

## <a name="configure-the-external-stream-input-and-output-objects"></a>外部ストリームの入力オブジェクトと出力オブジェクトを構成する

T-SQL ストリーミングでは、SQL Server の外部データ ソース機能を使用して、ストリーミング ジョブの外部ストリームの入力と出力に関連付けるデータ ソースを定義します。 外部ストリームの入力オブジェクトまたは出力オブジェクトを作成するには、次の T-SQL コマンドを使用します。

- [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql)

- [CREATE EXTERNAL DATA SOURCE (Transact-SQL)](/sql/t-sql/statements/create-external-data-source-transact-sql)

- [CREATE EXTERNAL STREAM (Transact-SQL)](#example-create-an-external-stream-object-to-azure-sql-database)

さらに、出力ストリームとして Azure SQL Edge、SQL Server、または Azure SQL Database が使用されている場合は、[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](/sql/t-sql/statements/create-database-scoped-credential-transact-sql) が必要です。 この T-SQL コマンドでは、データベースにアクセスするための資格情報を定義します。

### <a name="supported-input-and-output-stream-data-sources"></a>サポートされている入力ストリームと出力ストリームのデータ ソース

現在、Azure SQL Edge でストリーム入力および出力としてサポートされているのは、次のデータ ソースのみです。

| [データ ソースの種類] | 入力 | 出力 | 説明 |
|------------------|-------|--------|------------------|
| Azure IoT Edge ハブ | Y | Y | Azure IoT Edge ハブに対するストリーミング データの読み書きを行うためのデータ ソース。 詳細については、[IoT Edge ハブ](../iot-edge/iot-edge-runtime.md#iot-edge-hub)に関するページを参照してください。|
| SQL Database | N | Y | SQL Database にストリーミング データを書き込むためのデータ ソース接続。 データベースは、Azure SQL Edge のローカル データベースでも、SQL Server または Azure SQL Database のリモート データベースでもかまいません。|
| Kafka | Y | N | Kafka トピックからストリーミング データを読み取るためのデータ ソース。 現在、このアダプターは、Azure SQL Edge の Intel または AMD バージョンでのみ使用できます。 ARM64 バージョンの Azure SQL Edge では使用できません。|

### <a name="example-create-an-external-stream-inputoutput-object-for-azure-iot-edge-hub"></a>例:Azure IoT Edge ハブ用の外部ストリームの入力オブジェクトと出力オブジェクトを作成する

次の例では、Azure IoT Edge ハブ用の外部ストリーム オブジェクトを作成します。 Azure IoT Edge ハブ用の外部ストリームの入力および出力データ ソースを作成するには、最初に読み書きされるデータのレイアウトに対する外部ファイル形式を作成する必要もあります。

1. JSON 型の外部ファイル形式を作成します。

    ```sql
    Create External file format InputFileFormat
    WITH 
    (  
       format_type = JSON,
    )
    go
    ```

2. Azure IoT Edge ハブに対する外部データ ソースを作成します。 次の T-SQL スクリプトでは、Azure SQL Edge と同じ Docker ホスト上で実行される IoT Edge ハブへのデータ ソース接続が作成されます。

    ```sql
    CREATE EXTERNAL DATA SOURCE EdgeHubInput 
    WITH 
    (
        LOCATION = 'edgehub://'
    )
    go
    ```

3. Azure IoT Edge ハブに対する外部ストリーム オブジェクトを作成します。 次の T-SQL スクリプトでは、IoT Edge ハブに対するストリーム オブジェクトが作成されます。 IoT Edge ハブのストリーム オブジェクトの場合、LOCATION パラメーターは、読み取り元または書き込み先である IoT Edge ハブのトピックおよびチャネルの名前です。

    ```sql
    CREATE EXTERNAL STREAM MyTempSensors 
    WITH 
    (
        DATA_SOURCE = EdgeHubInput,
        FILE_FORMAT = InputFileFormat,
        LOCATION = N'TemperatureSensors',
        INPUT_OPTIONS = N'',
        OUTPUT_OPTIONS = N''
    );
    go
    ```

### <a name="example-create-an-external-stream-object-to-azure-sql-database"></a>例:Azure SQL Database への外部ストリーム オブジェクトを作成する

次の例では、Azure SQL Edge のローカル データベースに対する外部ストリーム オブジェクトを作成します。 

1. データベースにマスター キーを作成します。 これは、資格情報のシークレットの暗号化に必須です。

    ```sql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<<Strong_Password_For_Master_Key_Encryption>>';
    ```

2. SQL Server ソースにアクセスするために、データベース スコープの資格情報を作成します。 次の例では、IDENTITY = username と SECRET = password を使用して、外部データ ソースに対する資格情報を作成します。

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL SQLCredential
    WITH IDENTITY = '<SQL_Login>', SECRET = '<SQL_Login_PASSWORD>'
    go
    ```

3. CREATE EXTERNAL DATA SOURCE を使用して、外部データ ソースを作成します。 次に例を示します。

    * *LocalSQLOutput* という名前の外部データ ソースを作成します。
    * 外部データ ソースを識別します (LOCATION = '<vendor>://<server>[:<port>]'). この例では、Azure SQL Edge のローカル インスタンスを指しています。
    * 前に作成した資格情報を使用します。

    ```sql
    CREATE EXTERNAL DATA SOURCE LocalSQLOutput 
    WITH 
    (
        LOCATION = 'sqlserver://tcp:.,1433',
        CREDENTIAL = SQLCredential
    )
    go
    ```

4. 外部ストリーム オブジェクトを作成します。 次の例では、データベース *MySQLDatabase* の *dbo.TemperatureMeasurements* テーブルを指す外部ストリーム オブジェクトを作成します。

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurements 
    WITH 
    (
        DATA_SOURCE = LocalSQLOutput,
        LOCATION = N'MySQLDatabase.dbo.TemperatureMeasurements',
        INPUT_OPTIONS = N'',
        OUTPUT_OPTIONS = N''
    );
    ```

### <a name="example-create-an-external-stream-object-for-kafka"></a>例:Kafka 用の外部ストリーム オブジェクトを作成する

次の例では、Azure SQL Edge のローカル データベースに対する外部ストリーム オブジェクトを作成します。 この例では、Kafka サーバーが匿名アクセス用に構成されていることを前提としています。 

1. CREATE EXTERNAL DATA SOURCE を使用して、外部データ ソースを作成します。 次に例を示します。

    ```sql
    Create EXTERNAL DATA SOURCE [KafkaInput] 
    With
    (
        LOCATION = N'kafka://<kafka_bootstrap_server_name_ip>:<port_number>'
    )
    GO
    ```
2. Kafka 入力用の外部ファイル形式を作成します。 次の例では、Gzip 圧縮を使用して JSON ファイル形式を作成しました。 

   ```sql
   CREATE EXTERNAL FILE FORMAT JsonGzipped  
    WITH 
    (  
        FORMAT_TYPE = JSON , 
        DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec' 
    )
   ```

3. 外部ストリーム オブジェクトを作成します。 次の例では、Kafka トピック `*TemperatureMeasurement*` を指す外部ストリーム オブジェクトを作成します。

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurement 
    WITH 
    (  
        DATA_SOURCE = KafkaInput, 
        FILE_FORMAT = JsonGzipped,
        LOCATION = 'TemperatureMeasurement',
        INPUT_OPTIONS = 'PARTITIONS: 10' 
    ); 
    ```

## <a name="create-the-streaming-job-and-the-streaming-queries"></a>ストリーミング ジョブとストリーミング クエリを作成する

`sys.sp_create_streaming_job` システム ストアド プロシージャを使用して、ストリーミング クエリを定義し、ストリーミング ジョブを作成します。 `sp_create_streaming_job` ストアド プロシージャは、次のパラメーターを受け取ります。

- `job_name`:ストリーミング ジョブの名前。 ストリーミング ジョブの名前は、インスタンス全体で一意です。
- `statement`:[Stream Analytics クエリ言語](/stream-analytics-query/stream-analytics-query-language-reference)ベースのストリーミング クエリ ステートメント。

次の例では、1 つのストリーミング クエリを使用して簡単なストリーミング ジョブを作成します。 このクエリでは、IoT Edge ハブから入力を読み取り、データベースの `dbo.TemperatureMeasurements` に書き込みます。

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob1',
@statement= N'Select * INTO TemperatureMeasurements from MyEdgeHubInput'
```

次の例では、複数の異なるクエリを使用して、さらに複雑なストリーミング ジョブを作成します。 これらのクエリには、組み込みの `AnomalyDetection_ChangePoint` 関数を使用して温度データの異常を特定するものが含まれます。

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob2', @statement=
N' Select * INTO TemperatureMeasurements1 from MyEdgeHubInput1

Select * Into TemperatureMeasurements2 from MyEdgeHubInput2

Select * Into TemperatureMeasurements3 from MyEdgeHubInput3

SELECT
Timestamp as [Time],
[Temperature] As [Temperature],
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''Score'') as ChangePointScore,
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''IsAnomaly'') as IsChangePointAnomaly
INTO TemperatureAnomalies FROM MyEdgeHubInput2;
'
go
```

## <a name="start-stop-drop-and-monitor-streaming-jobs"></a>ストリーミング ジョブを開始、停止、削除、監視する

Azure SQL Edge でストリーミング ジョブを開始するには、`sys.sp_start_streaming_job` ストアド プロシージャを実行します。 このストアド プロシージャでは、開始するストリーミング ジョブの名前が入力として必要です。

```sql
exec sys.sp_start_streaming_job @name=N'StreamingJob1'
go
```

ストリーミング ジョブを停止するには、`sys.sp_stop_streaming_job` ストアド プロシージャを実行します。 このストアド プロシージャでは、入力として停止するストリーミング ジョブの名前が必要です。

```sql
exec sys.sp_stop_streaming_job @name=N'StreamingJob1'
go
```

ストリーミング ジョブをドロップ (削除) するには、`sys.sp_drop_streaming_job` ストアド プロシージャを実行します。 このストアド プロシージャでは、削除するストリーミング ジョブの名前が入力として必要です。

```sql
exec sys.sp_drop_streaming_job @name=N'StreamingJob1'
go
```

ストリーミング ジョブの現在の状態を取得するには、`sys.sp_get_streaming_job` ストアド プロシージャを実行します。 このストアド プロシージャでは、削除するストリーミング ジョブの名前が入力として必要です。 ストリーミング ジョブの名前と現在の状態が出力されます。

```sql
exec sys.sp_get_streaming_job @name=N'StreamingJob1'
        WITH RESULT SETS
(
       (
       name nvarchar(256),
       status nvarchar(256),
       error nvarchar(256)
       )
)
```

ストリーミング ジョブの状態は次のいずれかです。

| Status | 説明 |
|--------| ------------|
| 作成済み | ストリーミング ジョブは作成されましたが、まだ開始されていません。 |
| 開始中 | ストリーミング ジョブは開始フェーズになっています。 |
| アイドル | ストリーミング ジョブは実行中ですが、処理する入力がありません。 |
| 処理中 | ストリーミング ジョブは実行中であり、入力を処理しています。 この状態は、ストリーミング ジョブが正常な状態であることを示します。 |
| 低下しています | ストリーミング ジョブは実行中ですが、入力処理の間に致命的ではないエラーが発生しました。 入力ジョブは実行を続けますが、エラーが発生する入力は削除されます。 |
| 停止済み | ストリーミング ジョブは停止されました。 |
| 失敗 | ストリーミング ジョブは失敗しました。 これは、通常、処理中に致命的なエラーが発生したことを示します。 |

## <a name="next-steps"></a>次のステップ

- [Azure SQL Edge でストリーミング ジョブに関連付けられたメタデータを表示する](streaming-catalog-views.md) 
- [外部ストリームを作成する](create-external-stream-transact-sql.md)