---
title: SAP テーブルからデータをコピーする
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、SAP テーブルからサポートされているシンク データ ストアへデータをコピーする方法について説明します。
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/03/2021
ms.openlocfilehash: 63509262b8a75eebaffc34eca9861fe6748ff969
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048456"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Azure Data Factory を使用して SAP テーブルからデータをコピーする

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory のコピー アクティビティを使用して、SAP テーブルからデータをコピーする方法について説明します。 詳細については、[コピー アクティビティの概要](copy-activity-overview.md)に関するページを参照してください。

>[!TIP]
>SAP データ統合シナリオにおける ADF の全体的なサポートについては、各 SAP コネクタの詳細な情報、比較、およびガイダンスが含まれる、[Azure Data Factory を使用した SAP データの統合に関するホワイトペーパー](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf)を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

この SAP テーブル コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

SAP テーブルから、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表を参照してください。

具体的には、この SAP テーブル コネクタは以下をサポートします。

- 次の場所の SAP テーブルからのデータのコピー:

  - SAP ERP Central Component (SAP ECC) バージョン 7.01 以降 (2015 年以後にリリースされた最近の SAP サポート パッケージ スタック)。
  - SAP Business Warehouse (SAP BW) バージョン 7.01 以降 (2015 年よりも後にリリースされた最新の SAP サポート パッケージ スタック)。
  - SAP S/4HANA。
  - SAP Business Suite バージョン 7.01 以降のその他の製品 (2015 年よりも後にリリースされた最近の SAP サポート パッケージ スタック)。

- SAP 透過テーブル、プールされたテーブル、クラスター化されたテーブル、およびビューの両方からのデータのコピー。
- 基本認証、または Secure Network Communications (SNC) が構成されている場合は、SNC を使用したデータのコピー。
- SAP アプリケーション サーバーまたは SAP メッセージ サーバーへの接続。
- 既定の RFC またはカスタム RFC によるデータの取得。

バージョン 7.01 以降は、SAP ECC のバージョンではなく SAP NetWeaver のバージョンを指します。 たとえば、一般に SAP ECC 6.0 EHP 7 の NetWeaver バージョンは 7.4 以降です。 お使いの環境が不明な場合に、SAP システムからバージョンを確認する手順を次に示します。

1. SAP GUI を使用して SAP システムに接続します。 
2. **[システム]**  ->  **[ステータス]** に移動します。 
3. SAP_BASIS のリリースを調べ、701 以上であることを確認します。  
      ![SAP_BASIS の確認](./media/connector-sap-table/sap-basis.png)

## <a name="prerequisites"></a>前提条件

この SAP テーブル コネクタを使用するには、次の手順を実行する必要があります。

- セルフホステッド統合ランタイム (バージョン 3.17 以降) をセットアップします。 詳細については、「[セルフホステッド統合ランタイムを作成して共有する](create-self-hosted-integration-runtime.md)」を参照してください。

- [Microsoft .NET 3.0 用の 64 ビット SAP コネクタ](https://support.sap.com/en/product/connectors/msnet.html)を SAP の Web サイトからダウンロードし、それをセルフホステッド統合ランタイムのコンピューターにインストールします。 インストール時に、 **[Optional setup steps]\(省略可能なセットアップ手順\)** ウィンドウで、 **[Install Assemblies to GAC]\(アセンブリを GAC にインストールする\)** オプションを必ず選択します。

  ![SAP Connector for .NET をインストールする](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Data Factory の SAP テーブル コネクタで使用される SAP ユーザーは、以下のアクセス許可を持っている必要があります。

  - Remote Function Call (RFC) の宛先を使用するための認可。
  - S_SDSAUTH 認可オブジェクトの "実行" アクティビティに対するアクセス許可。 大半の認可オブジェクトについては、SAP Note 40089 を参照できます。 RFC_FUNCTION_SEARCH など、基礎となる NCo コネクタでは特定の RFC が要求されます。 

## <a name="get-started"></a>はじめに

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以降のセクションでは、SAP テーブル コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

SAP BW オープン ハブのリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| `type` | `type` プロパティは `SapTable` に設定する必要があります。 | はい |
| `server` | SAP インスタンスがあるサーバーの名前。<br/>SAP アプリケーション サーバーへの接続に使用されます。 | いいえ |
| `systemNumber` | SAP システムのシステム番号。<br/>SAP アプリケーション サーバーへの接続に使用されます。<br/>許可されている値:2 桁の 10 進数の文字列として表されます。 | いいえ |
| `messageServer` | SAP メッセージ サーバーのホスト名。<br/>SAP メッセージ サーバーへの接続に使用されます。 | いいえ |
| `messageServerService` | メッセージ サーバーのサービス名またはポート番号。<br/>SAP メッセージ サーバーへの接続に使用されます。 | いいえ |
| `systemId` | テーブルが配置されている SAP システムの ID。<br/>SAP メッセージ サーバーへの接続に使用されます。 | いいえ |
| `logonGroup` | SAP システムのログオン グループ。<br/>SAP メッセージ サーバーへの接続に使用されます。 | いいえ |
| `clientId` | SAP システム内のクライアントの ID。<br/>許可されている値:3 桁の 10 進数の文字列として表されます。 | はい |
| `language` | SAP システムで使用する言語。<br/>既定値は `EN` です。| いいえ |
| `userName` | SAP サーバーにアクセスできるユーザーの名前。 | はい |
| `password` | ユーザーのパスワードです。 このフィールドを `SecureString` 型でマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | はい |
| `sncMode` | テーブルが配置されている SAP サーバーにアクセスするための SNC アクティブ化インジケーター。<br/>SNC を使用して SAP サーバーに接続する場合に使用されます。<br/>使用できる値は、`0` (オフ、既定値) または `1` (オン) です。 | いいえ |
| `sncMyName` | テーブルが配置されている SAP サーバーにアクセスするためのイニシエーターの SNC 名。<br/>`sncMode` がオンのときに適用されます。 | いいえ |
| `sncPartnerName` | テーブルが配置されている SAP サーバーにアクセスするための通信パートナーの SNC 名。<br/>`sncMode` がオンのときに適用されます。 | いいえ |
| `sncLibraryPath` | テーブルが配置されている SAP サーバーにアクセスするための外部セキュリティ製品のライブラリ。<br/>`sncMode` がオンのときに適用されます。 | いいえ |
| `sncQop` | 適用する SNC の保護品質レベル。<br/>`sncMode` がオンのときに適用されます。 <br/>使用できる値は、`1` (認証)、`2` (整合性)、`3` (プライバシー)、`8` (既定)、`9` (最大) です。 | いいえ |
| `connectVia` | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 「[前提条件](#prerequisites)」で前述されているように、セルフホステッド統合ランタイムが必要です。 |はい |

### <a name="example-1-connect-to-an-sap-application-server"></a>例 1: SAP アプリケーション サーバーに接続する

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-2-connect-to-an-sap-message-server"></a>例 2:SAP メッセージ サーバーに接続する

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system ID>",
            "logonGroup": "<logon group>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-3-connect-by-using-snc"></a>例 3: SNC を使用して接続する

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "<SNC myname>",
            "sncPartnerName": "<SNC partner name>",
            "sncLibraryPath": "<SNC library path>",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するためのセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関するページをご覧ください。 次のセクションでは、SAP テーブル データセットでサポートされるプロパティの一覧を示します。

SAP BW オープン ハブのリンクされたサービスとの間でデータをコピーするために、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| `type` | `type` プロパティは `SapTableResource` に設定する必要があります。 | はい |
| `tableName` | データのコピー元である SAP テーブルの名前。 | はい |

### <a name="example"></a>例

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "typeProperties": {
            "tableName": "<SAP table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティを定義するためのセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関するページをご覧ください。 次のセクションでは、SAP テーブル ソースでサポートされるプロパティの一覧を示します。

### <a name="sap-table-as-source"></a>ソースとしての SAP テーブル

SAP テーブルからデータをコピーするために、次のプロパティがサポートされています。

| プロパティ                         | 説明                                                  | 必須 |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | `type` プロパティは `SapTableSource` に設定する必要があります。         | はい      |
| `rowCount`                         | 取得する行の数。                              | いいえ       |
| `rfcTableFields`                 | SAP テーブルからコピーするフィールド (列)。 たとえば、「 `column0, column1` 」のように入力します。 | いいえ       |
| `rfcTableOptions`                | SAP テーブルの行をフィルターにかけるためのオプション。 たとえば、「 `COLUMN0 EQ 'SOMEVALUE'` 」のように入力します。 この記事で後に提供する SAP クエリ演算子の表も参照してください。 | いいえ       |
| `customRfcReadTableFunctionModule` | SAP テーブルからデータを読み取るために使用できるカスタム RFC 関数モジュール。<br>カスタム RFC 関数モジュールを使用して、SAP システムからデータを取得して Data Factory に返す方法を定義できます。 カスタム関数モジュールでは、`/SAPDS/RFC_READ_TABLE2` と同様のインターフェイスが実装されている必要があります (インポート、エクスポート、テーブル)。これは、Data Factory で使用される既定のインターフェイスです。<br>Data Factory | いいえ       |
| `partitionOption`                  | SAP テーブルから読み取るパーティション メカニズム。 サポートされているオプションは次のとおりです。 <ul><li>`None`</li><li>`PartitionOnInt` (通常の整数値、または `0000012345` のように左側をゼロでパディングした整数値)</li><li>`PartitionOnCalendarYear` ("YYYY" の形式の 4 桁の数字)</li><li>`PartitionOnCalendarMonth` ("YYYYMM" の形式の 6 桁の数字)</li><li>`PartitionOnCalendarDate` ("YYYYMMDD" の形式の 8 桁の数字)</li><li>`PartitionOntime` ("HHMMSS" の形式の 6 桁の数字、`235959` など)</li></ul> | いいえ       |
| `partitionColumnName`              | データを分割するために使用される列の名前。                | いいえ       |
| `partitionUpperBound`              | `partitionColumnName` で指定され、パーティション分割を続行するために使用される列の最大値。 | いいえ       |
| `partitionLowerBound`              | `partitionColumnName` で指定され、パーティション分割を続行するために使用される列の最小値。 (注: パーティション オプションが `PartitionOnInt` の場合、`partitionLowerBound` を "0" にすることはできません) | いいえ       |
| `maxPartitionsNumber`              | データを分割するパーティションの最大数。     | いいえ       |
| `sapDataColumnDelimiter` | 出力データを分けるために SAP RFC に渡される、区切り記号として使用される 1 文字。 | いいえ |

>[!TIP]
>SAP テーブルに大量のデータ (数十億行など) がある場合は、`partitionOption` と `partitionSetting` を使ってデータをより小さいパーティションに分割します。 この場合、データはパーティションごとに読み取られ、各データ パーティションは、単一の RCF 呼び出しによって SAP サーバーから取得されます。<br/>
<br/>
>例として `partitionOption` を `partitionOnInt` として取ると、各パーティション内の行数はこの式を使って計算されます: (`partitionUpperBound` と `partitionLowerBound` の間の合計行数)/`maxPartitionsNumber`。<br/>
<br/>
>コピーを高速化するためにデータ パーティションを並行して読み込むために、並列度はコピー アクティビティの [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 設定によって制御されます。 たとえば、`parallelCopies` を 4 に設定した場合、Data Factory では、指定したパーティション オプションと設定に基づいて 4 つのクエリが同時に生成され、実行されます。各クエリは、SAP テーブルからデータの一部を取得します。 `maxPartitionsNumber` を `parallelCopies` プロパティの値の倍数にすることを強くお勧めします。 ファイルベースのデータ ストアにデータをコピーする場合は、複数のファイルとしてフォルダーに書き込む (フォルダー名のみを指定する) こともお勧めします。この場合、1 つのファイルに書き込むよりもパフォーマンスが優れています。

`rfcTableOptions` では、次の一般的な SAP クエリ演算子を使用して行をフィルター処理できます。

| 演算子 | 説明 |
| :------- | :------- |
| `EQ` | 等しい |
| `NE` | 等しくない |
| `LT` | より小さい |
| `LE` | 以下 |
| `GT` | より大きい |
| `GE` | 以上 |
| `IN` | `TABCLASS IN ('TRANSP', 'INTTAB')` と同様 |
| `LIKE` | `LIKE 'Emma%'` と同様 |

### <a name="example"></a>例

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP table input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapTableSource",
                "partitionOption": "PartitionOnInt",
                "partitionSettings": {
                     "partitionColumnName": "<partition column name>",
                     "partitionUpperBound": "2000",
                     "partitionLowerBound": "1",
                     "maxPartitionsNumber": 500
                 }
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="join-sap-tables"></a>SAP テーブルを結合する

現在、SAP テーブル コネクタでは、既定の関数モジュールを備えた単一のテーブルのみがサポートされます。 複数のテーブルの結合されたデータを取得するには、次の手順に従って、SAP テーブル コネクタの [customRfcReadTableFunctionModule](#copy-activity-properties) プロパティを利用してください。

- クエリを OPTIONS として処理し、独自のロジックを適用してデータを取得できるようにする、[カスタム関数モジュールを記述します](#create-custom-function-module)。
- [Custom function module]\(カスタム関数モジュール\) で、自分のカスタム関数モジュールの名前を入力します。
- [RFC table options]\(RFC テーブル オプション) で、自分の関数モジュールに取り込まれるテーブル結合ステートメントを OPTIONS として指定します。たとえば、"`<TABLE1>` INNER JOIN `<TABLE2>` ON COLUMN0"。

次に例を示します。

![SAP テーブルの結合](./media/connector-sap-table/sap-table-join.png) 

>[!TIP]
>また、SAP テーブル コネクタでサポートされている VIEW で、結合されたデータを集計することも検討できます。
>また、関連テーブルを抽出して Azure (Azure Storage、Azure SQL Database など) にオンボードしてみてから、Data Flow を使用して、さらに結合またはフィルター処理に進むことができます。

## <a name="create-custom-function-module"></a>カスタム関数モジュールを作成する

SAP テーブルについては、現在、[customRfcReadTableFunctionModule](#copy-activity-properties) プロパティをコピー ソースでサポートしています。これにより、独自のロジックを活用して、データを処理することができます。

ここでは、"カスタム関数モジュール" の使用を開始するためのいくつかの要件について簡単に説明します。

- 定義は次のとおりです。

    ![定義](./media/connector-sap-table/custom-function-module-definition.png) 

- 次のいずれかのテーブルにデータをエクスポートします。

    ![エクスポート テーブル 1](./media/connector-sap-table/export-table-1.png) 

    ![エクスポート テーブル 2](./media/connector-sap-table/export-table-2.png)
 
SAP テーブル コネクタでカスタム関数モジュールがどのように使用されるのかを以下に示します。

1. SAP NCO を経由する SAP サーバーとの接続を構築します。

1. パラメーターを次のように設定して、"カスタム関数モジュール" を呼び出します。

    - QUERY_TABLE: ADF SAP テーブル データセットで設定したテーブル名。 
    - Delimiter: ADF SAP テーブル ソースで設定した区切り記号。 
    - ROWCOUNT/Option/Fields: ADF テーブル ソースで設定した行数/集計オプション/フィールド。

1. 次の方法で結果を取得してデータを解析します。

    1. Fields テーブル内の値を解析して、スキーマを取得します。

        ![Fields 内の値を解析する](./media/connector-sap-table/parse-values.png)

    1. 出力テーブルの値を取得して、これらの値が含まれているテーブルを確認します。

        ![出力テーブル内の値を取得する](./media/connector-sap-table/get-values.png)

    1. OUT_TABLE 内の値を取得し、データを解析してから、シンクに書き込みます。

## <a name="data-type-mappings-for-an-sap-table"></a>SAP テーブルのデータ型マッピング

SAP テーブルからデータをコピーするとき、SAP テーブルのデータ型から Azure Data Factory の中間データ型への次のマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| SAP ABAP の型 | Data Factory の中間データ型 |
|:--- |:--- |
| `C` (String) | `String` |
| `I` (Integer) | `Int32` |
| `F` (Float) | `Double` |
| `D` (Date) | `String` |
| `T` (Time) | `String` |
| `P` (BCD Packed、Currency、Decimal、Qty) | `Decimal` |
| `N` (Numeric) | `String` |
| `X` (Binary および Raw) | `String` |

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するページを参照してください。
