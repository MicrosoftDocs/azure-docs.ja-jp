---
title: Azure Data Factory を使用した SAP テーブルからのデータ コピー | Microsoft Docs
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、SAP テーブルからサポートされているシンク データ ストアへデータをコピーする方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: jingwang
ms.openlocfilehash: e54a69b6c2b48e50c089f8b6b7458cf91133dd85
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443300"
---
# <a name="copy-data-from-sap-table-using-azure-data-factory"></a>Azure Data Factory を使用して SAP テーブルからデータをコピーする

この記事では、Azure Data Factory のコピー アクティビティを使用して、SAP テーブルからデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

SAP テーブルから、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この SAP テーブル コネクタは以下をサポートします。

- 次の場所の SAP テーブルからのデータのコピー:

    - **SAP ECC** バージョン 7.01 以降 (2015 年以後にリリースされた最近の SAP サポート パッケージ スタックの場合)
    - **SAP BW** バージョン 7.01 以降
    - **SAP S/4HANa**
    - **SAP Business Suite の他の製品**のバージョン 7.01 以降 

- **SAP 透過テーブル**と**ビュー**の両方からのデータ コピー。
- **基本認証**または **SNC** (Secure Network Communications) (SNC が構成されている場合) を使用したデータ コピー。
- **Application Server** または **Message Server** への接続。

## <a name="prerequisites"></a>前提条件

この SAP テーブル コネクタを使用するには、次の手順が必要です。

- バージョン 3.17 以上のセルフホステッド統合ランタイムをセットアップする。 詳細については、[セルフホステッド統合ランタイム](create-self-hosted-integration-runtime.md)に関する記事をご覧ください。

- SAP の Web サイトから **64 ビットの [SAP .NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html)** をダウンロードし、それをセルフホステッド IR マシンにインストールします。 インストール時に、省略可能なセットアップ手順ウィンドウで、 **[Install Assemblies to GAC]\(アセンブリを GAC にインストールする\)** オプションを必ず選択します。 

    ![SAP .NET Connector をインストールする](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Data Factory の SAP テーブル コネクタで使用する予定の SAP ユーザーは、以下のアクセス許可を持っている必要があります。 

    - RFC の認可。 
    - 認可オブジェクト "S_SDSAUTH" の "実行" アクティビティに対するアクセス許可。

## <a name="getting-started"></a>使用の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、SAP テーブル コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

SAP Business Warehouse オープン ハブのリンクされたサービスでは、以下のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**SapTable** | はい |
| サーバー | SAP インスタンスが存在するサーバーの名前。<br/>**SAP アプリケーション サーバー**に接続する場合に使用されます。 | いいえ |
| systemNumber | SAP システムのシステム番号。<br/>**SAP アプリケーション サーバー**に接続する場合に使用されます。<br/>使用できる値: 文字列として表される 2 桁の 10 進数。 | いいえ |
| messageServer | SAP メッセージ サーバーのホスト名。<br/>**SAP メッセージ サーバー**に接続する場合に使用されます。 | いいえ |
| messageServerService | メッセージ サーバーのサービス名またはポート番号。<br/>**SAP メッセージ サーバー**に接続する場合に使用されます。 | いいえ |
| systemId | テーブルが配置されている SAP システムのシステム ID。<br/>**SAP メッセージ サーバー**に接続する場合に使用されます。 | いいえ |
| logonGroup | SAP システムのログオン グループ。<br/>**SAP メッセージ サーバー**に接続する場合に使用されます。 | いいえ |
| clientId | SAP システム内のクライアントのクライアント ID。<br/>使用できる値: 文字列として表される 3 桁の 10 進数。 | はい |
| language | SAP システムで使用する言語。 | いいえ (既定値は **EN**)|
| userName | SAP サーバーにアクセスできるユーザーの名前。 | はい |
| password | ユーザーのパスワード。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | はい |
| sncMode | テーブルが配置されている SAP サーバーにアクセスするための SNC アクティブ化インジケーター。<br/>SNC を使用して SAP サーバーに接続する場合に使用されます。<br/>使用できる値は、以下のとおりです。**0** (オフ、既定値) または **1** (オン)。 | いいえ |
| sncMyName | テーブルが配置されている SAP サーバーにアクセスするためのイニシエーターの SNC 名。<br/>`sncMode` がオンの場合に使用されます。 | いいえ |
| sncPartnerName | テーブルが配置されている SAP サーバーにアクセスするための通信パートナーの SNC 名。<br/>`sncMode` がオンの場合に使用されます。 | いいえ |
| sncLibraryPath | テーブルが配置されている SAP サーバーにアクセスするための外部セキュリティ製品のライブラリ。<br/>`sncMode` がオンの場合に使用されます。 | いいえ |
| sncQop | 保護の SNC 品質。<br/>`sncMode` がオンの場合に使用されます。 <br/>使用できる値は、以下のとおりです。**1** (認証)、**2** (整合性)、**3** (プライバシー)、**8** (既定)、**9** (最大)。 | いいえ |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 「[前提条件](#prerequisites)」に記されているように、セルフホステッド統合ランタイムが必要です。 |はい |

**例 1: SAP アプリケーション サーバーへの接続**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
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

**例 2: SAP メッセージ サーバーへの接続**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system id>",
            "logonGroup": "<logon group>",
            "clientId": "<client id>",
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

**例 3: SNC を使用した接続**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "snc myname",
            "sncPartnerName": "snc partner name",
            "sncLibraryPath": "snc library path",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、SAP テーブル データセットでサポートされるプロパティの一覧を示します。

SAP BW オープン ハブとの間でのデータ コピーについては、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **SapTableResource** に設定する必要があります。 | はい |
| tableName | データのコピー元である SAP テーブルの名前。 | はい |

**例:**

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "linkedServiceName": {
            "referenceName": "<SAP Table linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<SAP table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、SAP テーブル ソースでサポートされるプロパティの一覧を示します。

### <a name="sap-table-as-source"></a>ソースとしての SAP テーブル

SAP テーブルからのデータ コピーについては、次のプロパティがサポートされています。

| プロパティ                         | 説明                                                  | 必須 |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| type                             | type プロパティは **SapTableSource** に設定する必要があります。         | はい      |
| rowCount                         | 取得する行の数。                              | いいえ       |
| rfcTableFields                   | SAP テーブルからコピーするフィールド。 たとえば、「 `column0, column1` 」のように入力します。 | いいえ       |
| rfcTableOptions                  | SAP テーブルの行をフィルター選択するオプション。 たとえば、「 `COLUMN0 EQ 'SOMEVALUE'` 」のように入力します。 この表の後にある詳細な説明を参照してください。 | いいえ       |
| customRfcReadTableFunctionModule | SAP テーブルからデータを読み取るために使用できるカスタム RFC 関数モジュール。<br>カスタム RFC 関数モジュールを使用して、SAP システムからデータを取得して ADF に返す方法を定義できます。 ただし、カスタム関数モジュールでは同様のインターフェイス (インポート、エクスポート、テーブル) を実装する必要があることに注意してください (ADF によって既定で使用される /SAPDS/RFC_READ_TABLE2 と似たもの)。 | いいえ       |
| partitionOption                  | SAP テーブルから読み取るパーティション メカニズム。 サポートされているオプションは次のとおりです。 <br/>- **None**<br/>- **PartitionOnInt** (通常の整数値、または 0000012345 のように左側をゼロでパディングした整数値)<br/>- **PartitionOnCalendarYear** (4 桁の "YYYY" 形式)<br/>- **PartitionOnCalendarMonth** (6 桁の "YYYYMM" 形式)<br/>- **PartitionOnCalendarDate** (8 桁の "YYYYMMDD" 形式) | いいえ       |
| partitionColumnName              | データを分割する列の名前。                | いいえ       |
| partitionUpperBound              | `partitionColumnName` で指定され、パーティション分割を続行するために使用される列の最大値。 | いいえ       |
| partitionLowerBound              | `partitionColumnName` で指定され、パーティション分割を続行するために使用される列の最小値。 | いいえ       |
| maxPartitionsNumber              | データを分割するパーティションの最大数。     | いいえ       |

>[!TIP]
>- 数十億行などの大量のデータが SAP テーブルにある場合、`partitionOption` と `partitionSetting` を使用してデータを小さなパーティションに分割します。その場合、データはパーティションによって読み取られ、各データ パーティションが単一の RFC 呼び出しによって SAP サーバーから取得されます。<br/>
>- たとえば、`partitionOnInt` として `partitionOption` を受け取ると、各パーティションの行数は、*partitionUpperBound* と *partitionLowerBound* の間に収まる合計行数を *maxPartitionsNumber* で割って算出されます。<br/>
>- コピーを高速化するためにさらにパーティションを並列実行したい場合は、`maxPartitionsNumber` を `parallelCopies` の値の倍数にすることを強くお勧めします (詳しくは[並列コピー](copy-activity-performance.md#parallel-copy)を参照)。

`rfcTableOptions` では、たとえば次の一般的な SAP クエリ演算子を使用して、行をフィルター処理できます。 

| Operator | 説明 |
| :------- | :------- |
| EQ | 等しい |
| NE | 等しくない |
| LT | より小さい |
| LE | 以下 |
| GT | より大きい |
| GE | 以上 |
| LIKE | LIKE 'Emma%' として |

**例:**

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP Table input dataset name>",
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
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-table"></a>SAP テーブルのデータ型マッピング

SAP テーブルからデータをコピーするとき、次の SAP テーブルのデータ型から Azure Data Factory の中間データ型へのマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| SAP ABAP の型 | Data Factory の中間データ型 |
|:--- |:--- |
| C (String) | string |
| I (Integer) | Int32 |
| F (Float) | Double |
| D (Date) | string |
| T (Time) | string |
| P (BCD Packed、Currency、Decimal、Qty) | Decimal |
| N (Numeric) | string |
| X (Binary および Raw) | string |

## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
