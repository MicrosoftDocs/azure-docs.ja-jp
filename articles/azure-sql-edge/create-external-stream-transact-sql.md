---
title: CREATE EXTERNAL STREAM (Transact-SQL) - Azure SQL Edge
description: Azure SQL Edge での CREATE EXTERNAL STREAM ステートメントについて学習します
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/27/2020
ms.openlocfilehash: 203abe2b6def478dc1747dd4ce638b5b62707612
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101659224"
---
# <a name="create-external-stream-transact-sql"></a>CREATE EXTERNAL STREAM (Transact-SQL)

EXTERNAL STREAM オブジェクトには、入力ストリームと出力ストリームの両方の 2 つの目的があります。 これは、Azure Event Hub、Azure IoT Hub (または Edge ハブ)、Kafka などのイベント インジェスト サービスからのストリーミング データをクエリするための入力として使用することも、ストリーミング クエリの結果を格納する場所と方法を指定するための出力として使用することもできます。

EXTERNAL STREAM は、Event Hubs や Blob Storage などのサービスの出力と入力の両方として、指定および作成することもできます。 これにより、あるストリーミング クエリでは外部ストリームに対する結果が出力として保持されており、別のストリーミング クエリでは入力として同じ外部ストリームから読み取りが行われるチェーン シナリオが容易になります。

現在、Azure SQL Edge でストリーム入力および出力としてサポートされているのは、次のデータ ソースのみです。

| [データ ソースの種類] | 入力 | 出力 | 説明 |
|------------------|-------|--------|------------------|
| Azure IoT Edge ハブ | Y | Y | Azure IoT Edge ハブに対するストリーミング データの読み書きを行うためのデータ ソース。 詳細については、[IoT Edge ハブ](../iot-edge/iot-edge-runtime.md#iot-edge-hub)に関するページを参照してください。|
| SQL Database | N | Y | SQL Database にストリーミング データを書き込むためのデータ ソース接続。 データベースは、Azure SQL Edge のローカル データベースでも、SQL Server または Azure SQL Database のリモート データベースでもかまいません。|
| Kafka | Y | N | Kafka トピックからストリーミング データを読み取るためのデータ ソース。 Kafka サポートは、ARM64 バージョンの Azure SQL Edge では使用できません。|



## <a name="syntax"></a>構文

```sql
CREATE EXTERNAL STREAM {external_stream_name}  
( <column_definition> [, <column_definition> ] * ) -- Used for Inputs - optional 
WITH  ( <with_options> )

<column_definition> ::=
  column_name <column_data_type>

<data_type> ::=
[ type_schema_name . ] type_name
    [ ( precision [ , scale ] | max ) ]

<with_options> ::=
  DATA_SOURCE = data_source_name, 
  LOCATION = location_name, 
  [FILE_FORMAT = external_file_format_name], --Used for Inputs - optional 
  [<optional_input_options>],
  [<optional_output_options>], 
  TAGS = <tag_column_value>

<optional_input_options> ::= 
  INPUT_OPTIONS = '[<Input_options_data>]'

<Input_option_data> ::= 
      <input_option_values> [ , <input_option_values> ]

<input_option_values> ::=
  PARTITIONS: [number_of_partitions]
  | CONSUMER_GROUP: [ consumer_group_name ] 
  | TIME_POLICY: [ time_policy ] 
  | LATE_EVENT_TOLERANCE: [ late_event_tolerance_value ] 
  | OUT_OF_ORDER_EVENT_TOLERANCE: [ out_of_order_tolerance_value ]
  
<optional_output_options> ::= 
  OUTPUT_OPTIONS = '[<output_option_data>]'

<output_option_data> ::= 
      <output_option_values> [ , <output_option_values> ]

<output_option_values> ::=
   REJECT_POLICY: [ reject_policy ] 
   | MINIMUM_ROWS: [ row_value ] 
   | MAXIMUM_TIME: [ time_value_minutes] 
   | PARTITION_KEY_COLUMN: [ partition_key_column_name ] 
   | PROPERTY_COLUMNS: [ ( [ output_col_name ] ) ] 
   | SYSTEM_PROPERTY_COLUMNS: [ ( [ output_col_name ] ) ] 
   | PARTITION_KEY: [ partition_key_name ] 
   | ROW_KEY: [ row_key_name ] 
   | BATCH_SIZE: [ batch_size_value ] 
   | MAXIMUM_BATCH_COUNT: [ batch_value ] 
   | STAGING_AREA: [ blob_data_source ]
 
<tag_column_value> ::= -- Reserved for Future Usage
); 
```

## <a name="arguments"></a>引数

- [DATA_SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql/)
- [FILE_FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql/)
- **LOCATION**: データ ソース内の実際のデータまたは場所の名前を指定します。 
   - Edge ハブまたは Kafka ストリーム オブジェクトの場合、場所は読み取り元または書き込み先の Edge ハブまたは Kafka トピックの名前を指定します。
   - SQL ストリーム オブジェクト (SQL Server、Azure SQL Database または Azure SQL Edge) の場合、場所はテーブルの名前を指定します。 ターゲット テーブルと同じデータベースおよびスキーマにストリームが作成されている場合は、テーブル名だけで十分です。 それ以外の場合は、テーブル名を完全に修飾する必要があります (<database_name.schema_name.table_name)。
   - Azure Blob Storage の場合、ストリーム オブジェクトの場所とは、BLOB コンテナー内で使用するパス パターンを表します。 この機能の詳細については、(/articles/stream-analytics/stream-analytics-define-outputs.md#blob-storage-and-azure-data-lake-gen2) を参照してください。

- **INPUT_OPTIONS**: ストリーミング クエリに対する入力である Kafka や IoT Edge ハブなどのサービスへのオプションを、キーと値のペアとして指定します
    - PARTITIONS:トピックに対して定義されているパーティションの数。 使用できるパーティションの最大数は、32 に制限されています。
      - Kafka 入力ストリームに適用されます
    - CONSUMER_GROUP: Event Hubs や IOT Hub では、1 つのコンシューマー グループ内のリーダーの数が (5 個に) 制限されています。 このフィールドを空白のままにすると、"$Default" コンシューマー グループが使用されます。
      - 将来利用するために予約されています。 Azure SQL Edge には適用されません。  
    - TIME_POLICY: 遅延イベントまたは順不同イベントが許容範囲の値に到達したときに、イベントを削除するか、イベント時間を調整するかを指定します。
      - 将来利用するために予約されています。 Azure SQL Edge には適用されません。
    - LATE_EVENT_TOLERANCE: 許容される最大遅延時間。 遅延は、イベントのタイムスタンプとシステム クロックの間の差を表します。
      - 将来利用するために予約されています。 Azure SQL Edge には適用されません。
    - OUT_OF_ORDER_EVENT_TOLERANCE: イベントは、入力からストリーミング クエリに移動した後、順不同に到着してもかまいません。 このようなイベントは、そのまま受け入れることも、または設定した期間だけ一時停止して並べ替えることもできます。
      - 将来利用するために予約されています。 Azure SQL Edge には適用されません。
        
- **OUTPUT_OPTIONS**: ストリーミング クエリに対する出力であるサポートされるサービスへのオプションを、キーと値のペアとして指定します 
  - REJECT_POLICY: DROP | RETRY データ変換エラーが発生したときのデータ エラー処理ポリシーを指定します。 
    - サポートされるすべての出力に適用されます 
  - MINIMUM_ROWS:  
    出力に書き込まれるバッチごとに必要な最小行数。 Parquet では、バッチごとに新しいファイルが作成されます。 
    - サポートされるすべての出力に適用されます 
  - MAXIMUM_TIME:  
    バッチあたりの最大待機時間 (分)。 この時間が経過すると、最小行数の要件が満たされていなくても、バッチは出力に書き込まれます。 
    - サポートされるすべての出力に適用されます 
  - PARTITION_KEY_COLUMN:  
    パーティション キーに使用される列。 
    - 将来利用するために予約されています。 Azure SQL Edge には適用されません。
  - PROPERTY_COLUMNS:  
    提供されている場合、カスタム プロパティとしてメッセージに添付する出力列の名前のコンマ区切りのリスト。  
    - 将来利用するために予約されています。 Azure SQL Edge には適用されません。 
  - SYSTEM_PROPERTY_COLUMNS:  
    Service Bus メッセージに設定されるシステム プロパティ名と出力列の名前と値のペアの JSON 形式のコレクション。 例: { "MessageId":   "column1", "PartitionKey": "column2"} 
    - 将来利用するために予約されています。 Azure SQL Edge には適用されません。 
  - PARTITION_KEY:  
    パーティション キーが含まれる出力列の名前。 パーティション キーは、エンティティのプライマリ キーの最初の部分を形成する、特定のテーブル内のパーティションの一意の識別子です。 最大サイズが 1 KB の文字列値です。 
    - 将来利用するために予約されています。 Azure SQL Edge には適用されません。
  - ROW_KEY:  
    行キーが含まれる出力列の名前。 行キーは、特定のパーティション内のエンティティを示す一意の識別子です。 これにより、エンティティのプライマリ キーの 2 番目の部分が形成されます。 行キーは、最大サイズが 1 KB の文字列値です。 
    - 将来利用するために予約されています。 Azure SQL Edge には適用されません。
  - BATCH_SIZE:  
    これは、Table Storage に対するトランザクションの数を表します。最大 100 レコードまで可能です。 Azure Functions の場合、これは呼び出しごとに関数に送信されるバッチ サイズ (バイト単位) を表します。既定値は 256 KB です。 
    - 将来利用するために予約されています。 Azure SQL Edge には適用されません。 
  - MAXIMUM_BATCH_COUNT:  
    Azure 関数の場合は、呼び出しごとに関数に送信されるイベントの最大数。既定値は 100 です。 SQL Database の場合は、これは一括挿入の各トランザクションで送信されたレコードの最大数を表します。既定値は 10,000 です。 
    - すべての SQL ベースの出力に適用されます 
  - STAGING_AREA: Blob Storage への EXTERNAL DATA SOURCE オブジェクト。Azure Synapse Analytics への高スループット データ インジェストに対するステージング領域 
    - 将来利用するために予約されています。 Azure SQL Edge には適用されません。


## <a name="examples"></a>例

### <a name="example-1---edgehub"></a>例 1 - EdgeHub

型: 入力または出力<br>

構文:

```sql
CREATE EXTERNAL DATA SOURCE MyEdgeHub 
WITH  
(      
  LOCATION = 'edgehub://'       
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat  
WITH (  
   FORMAT_TYPE = JSON, 
); 
 
CREATE EXTERNAL STREAM Stream_A  
WITH    
(   
   DATA_SOURCE = MyEdgeHub, 
   FILE_FORMAT = myFileFormat, 
   LOCATION = '<mytopicname>', 
   OUTPUT_OPTIONS =   
     'REJECT_TYPE: Drop'
);
```

### <a name="example-2---azure-sql-database-azure-sql-edge-sql-server"></a>例 2 - Azure SQL Database、Azure SQL Edge、SQL Server

型: 出力<br>

構文:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>', 
SECRET = '<password>'; 
 
-- Azure SQL Database 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = '<my_server_name>.database.windows.net', 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Server or Azure SQL Edge
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <sqlserver://<ipaddress>,<port>', 
  CREDENTIAL = SQLCredName 
); 

CREATE EXTERNAL STREAM Stream_A 
WITH   
(  
    DATA_SOURCE = MyTargetSQLTable, 
    LOCATION = '<DatabaseName>.<SchemaName>.<TableName>' ,
   --Note: If table is contained in the database, <TableName> should be sufficient 
    OUTPUT_OPTIONS =  
      'REJECT_TYPE: Drop'
); 
```

### <a name="example-3---kafka"></a>例 3 - Kafka

型: 入力<br>

構文:
```sql
CREATE EXTERNAL DATA SOURCE MyKafka_tweets 
WITH 
( 
  --The location maps to KafkaBootstrapServer 
  LOCATION = 'kafka://<kafkaserver>:<ipaddress>', 
  CREDENTIAL = kafkaCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = JSON, 
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
); 

CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyKafka_tweets, 
    LOCATION = '<KafkaTopicName>', 
    FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      'PARTITIONS: 5'
); 
```

## <a name="see-also"></a>関連項目

- [DROP EXTERNAL STREAM (Transact-SQL)](drop-external-stream-transact-sql.md)
