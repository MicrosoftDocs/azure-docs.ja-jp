---
title: SAP テーブルからデータをコピーする
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、SAP テーブルからサポートされているシンク データ ストアへデータをコピーする方法について説明します。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2019
ms.openlocfilehash: fd363f7b685db5e309827a0c5e635264e676b388
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926192"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Azure Data Factory を使用して SAP テーブルからデータをコピーする

この記事では、Azure Data Factory のコピー アクティビティを使用して、SAP テーブルからデータをコピーする方法について説明します。 詳細については、[コピー アクティビティの概要](copy-activity-overview.md)に関するページを参照してください。

>[!TIP]
>SAP データ統合シナリオにおける ADF の全体的なサポートについては、[「Azure Data Factory を使用した SAP データの統合」ホワイトペーパー](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf)の詳細手順、比較、およびガイダンスを参照してください。

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

## <a name="prerequisites"></a>前提条件

この SAP テーブル コネクタを使用するには、次の手順を実行する必要があります。

- セルフホステッド統合ランタイム (バージョン 3.17 以降) をセットアップします。 詳細については、「[セルフホステッド統合ランタイムを作成して共有する](create-self-hosted-integration-runtime.md)」を参照してください。

- [Microsoft .NET 3.0 用の 64 ビット SAP コネクタ](https://support.sap.com/en/product/connectors/msnet.html)を SAP の Web サイトからダウンロードし、それをセルフホステッド統合ランタイムのコンピューターにインストールします。 インストール時に、 **[Optional setup steps]\(省略可能なセットアップ手順\)** ウィンドウで、 **[Install Assemblies to GAC]\(アセンブリを GAC にインストールする\)** オプションを必ず選択します。

  ![SAP Connector for .NET をインストールする](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Data Factory の SAP テーブル コネクタで使用される SAP ユーザーは、以下のアクセス許可を持っている必要があります。

  - Remote Function Call (RFC) の宛先を使用するための認可。
  - S_SDSAUTH 認可オブジェクトの "実行" アクティビティに対するアクセス許可。

## <a name="get-started"></a>作業開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以降のセクションでは、SAP テーブル コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

SAP BW オープン ハブのリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| `type` | `type` プロパティは、`SapTable` に設定する必要があります。 | はい |
| `server` | SAP インスタンスがあるサーバーの名前。<br/>SAP アプリケーション サーバーへの接続に使用されます。 | いいえ |
| `systemNumber` | SAP システムのシステム番号。<br/>SAP アプリケーション サーバーへの接続に使用されます。<br/>許可されている値:2 桁の 10 進数の文字列として表されます。 | いいえ |
| `messageServer` | SAP メッセージ サーバーのホスト名。<br/>SAP メッセージ サーバーへの接続に使用されます。 | いいえ |
| `messageServerService` | メッセージ サーバーのサービス名またはポート番号。<br/>SAP メッセージ サーバーへの接続に使用されます。 | いいえ |
| `systemId` | テーブルが配置されている SAP システムの ID。<br/>SAP メッセージ サーバーへの接続に使用されます。 | いいえ |
| `logonGroup` | SAP システムのログオン グループ。<br/>SAP メッセージ サーバーへの接続に使用されます。 | いいえ |
| `clientId` | SAP システム内のクライアントの ID。<br/>許可されている値:3 桁の 10 進数の文字列として表されます。 | はい |
| `language` | SAP システムで使用する言語。<br/>既定値は `EN` です。| いいえ |
| `userName` | SAP サーバーにアクセスできるユーザーの名前。 | はい |
| `password` | ユーザーのパスワード。 このフィールドを `SecureString` 型でマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | はい |
| `sncMode` | テーブルが配置されている SAP サーバーにアクセスするための SNC アクティブ化インジケーター。<br/>SNC を使用して SAP サーバーに接続する場合に使用されます。<br/>使用できる値は、`0` (オフ、既定値) または `1` (オン) です。 | いいえ |
| `sncMyName` | テーブルが配置されている SAP サーバーにアクセスするためのイニシエーターの SNC 名。<br/>`sncMode` がオンのときに適用されます。 | いいえ |
| `sncPartnerName` | テーブルが配置されている SAP サーバーにアクセスするための通信パートナーの SNC 名。<br/>`sncMode` がオンのときに適用されます。 | いいえ |
| `sncLibraryPath` | テーブルが配置されている SAP サーバーにアクセスするための外部セキュリティ製品のライブラリ。<br/>`sncMode` がオンのときに適用されます。 | いいえ |
| `sncQop` | 適用する SNC の保護品質レベル。<br/>`sncMode` がオンのときに適用されます。 <br/>使用できる値は、`1` (認証)、`2` (整合性)、`3` (プライバシー)、`8` (既定)、`9` (最大) です。 | いいえ |
| `connectVia` | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 「[前提条件](#prerequisites)」で前述されているように、セルフホステッド統合ランタイムが必要です。 |はい |

**例 1:SAP アプリケーション サーバーに接続する**

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

### <a name="example-3-connect-by-using-snc"></a>例 3:SNC を使用して接続する

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
| `type` | `type` プロパティは、`SapTableResource` に設定する必要があります。 | はい |
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
| `type`                             | `type` プロパティは、`SapTableSource` に設定する必要があります。         | はい      |
| `rowCount`                         | 取得する行の数。                              | いいえ       |
| `rfcTableFields`                   | SAP テーブルからコピーするフィールド (列)。 たとえば、「 `column0, column1` 」のように入力します。 | いいえ       |
| `rfcTableOptions`                  | SAP テーブルの行をフィルターにかけるためのオプション。 たとえば、「 `COLUMN0 EQ 'SOMEVALUE'` 」のように入力します。 この記事で後に提供する SAP クエリ演算子の表も参照してください。 | いいえ       |
| `customRfcReadTableFunctionModule` | SAP テーブルからデータを読み取るために使用できるカスタム RFC 関数モジュール。<br>カスタム RFC 関数モジュールを使用して、SAP システムからデータを取得して Data Factory に返す方法を定義できます。 カスタム関数モジュールでは、`/SAPDS/RFC_READ_TABLE2` と同様のインターフェイスが実装されている必要があります (インポート、エクスポート、テーブル)。これは、Data Factory で使用される既定のインターフェイスです。 | いいえ       |
| `partitionOption`                  | SAP テーブルから読み取るパーティション メカニズム。 サポートされているオプションは次のとおりです。 <ul><li>`None`</li><li>`PartitionOnInt` (通常の整数値、または `0000012345` のように左側をゼロでパディングした整数値)</li><li>`PartitionOnCalendarYear` ("YYYY" の形式の 4 桁の数字)</li><li>`PartitionOnCalendarMonth` ("YYYYMM" の形式の 6 桁の数字)</li><li>`PartitionOnCalendarDate` ("YYYYMMDD" の形式の 8 桁の数字)</li></ul> | いいえ       |
| `partitionColumnName`              | データを分割するために使用される列の名前。                | いいえ       |
| `partitionUpperBound`              | `partitionColumnName` で指定され、パーティション分割を続行するために使用される列の最大値。 | いいえ       |
| `partitionLowerBound`              | `partitionColumnName` で指定され、パーティション分割を続行するために使用される列の最小値。 | いいえ       |
| `maxPartitionsNumber`              | データを分割するパーティションの最大数。     | いいえ       |

>[!TIP]
>SAP テーブルに大量のデータ (数十億行など) がある場合は、`partitionOption` と `partitionSetting` を使ってデータをより小さいパーティションに分割します。 この場合、データはパーティションごとに読み取られ、各データ パーティションは、単一の RCF 呼び出しによって SAP サーバーから取得されます。<br/>
<br/>
>例として `partitionOption` を `partitionOnInt` として取ると、各パーティション内の行数はこの式を使って計算されます: (`partitionUpperBound` と `partitionLowerBound` の間の合計行数)/`maxPartitionsNumber`。<br/>
<br/>
>コピーを高速化するためにデータ パーティションを並行して読み込むために、並列度はコピー アクティビティの [`parallelCopies`](copy-activity-performance.md#parallel-copy) 設定によって制御されます。 たとえば、`parallelCopies` を 4 に設定した場合、Data Factory では、指定したパーティション オプションと設定に基づいて 4 つのクエリが同時に生成され、実行されます。各クエリは、SAP テーブルからデータの一部を取得します。 `maxPartitionsNumber` を `parallelCopies` プロパティの値の倍数にすることを強くお勧めします。 ファイルベースのデータ ストアにデータをコピーする場合は、複数のファイルとしてフォルダーに書き込む (フォルダー名のみを指定する) こともお勧めします。この場合、1 つのファイルに書き込むよりもパフォーマンスが優れています。

`rfcTableOptions` では、次の一般的な SAP クエリ演算子を使用して行をフィルター処理できます。

| Operator | 説明 |
| :------- | :------- |
| `EQ` | 等しい |
| `NE` | 等しくない |
| `LT` | より小さい |
| `LE` | 以下 |
| `GT` | より大きい |
| `GE` | 以上 |
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

## <a name="lookup-activity-properties"></a>ルックアップ アクティビティのプロパティ

プロパティの詳細については、[ルックアップ アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。


## <a name="next-steps"></a>次の手順

Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するページを参照してください。
