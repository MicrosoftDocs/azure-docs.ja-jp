---
title: SAP ECC からデータをコピーする
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、SAP ECC からサポートされているシンク データ ストアへデータをコピーする方法について説明します。
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/28/2020
ms.openlocfilehash: a3e701f3d433b5b52d8992035ac4ad75b78cb795
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100386698"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Azure Data Factory を使用して SAP ECC からデータをコピーする
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory のコピー アクティビティを使用して、SAP Enterprise Central Component (ECC) からデータをコピーする方法について説明します。 詳細については、[コピー アクティビティの概要](copy-activity-overview.md)に関するページを参照してください。

>[!TIP]
>SAP データ統合シナリオにおける ADF の全体的なサポートについては、各 SAP コネクタの詳細な情報、比較、およびガイダンスが含まれる、[Azure Data Factory を使用した SAP データの統合に関するホワイトペーパー](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf)を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

この SAP ECC コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

SAP ECC から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この SAP ECC コネクタは以下をサポートします。

- SAP NetWeaver バージョン 7.0 以降上の SAP ECC からのデータのコピー。
- SAP ECC OData サービスによって公開されているオブジェクトからのデータのコピー。次に例を示します。

  - SAP テーブルまたはビュー。
  - ビジネス アプリケーション プログラミング インターフェイス [BAPI] オブジェクト。
  - データ エクストラクター。
  - 相対アダプター経由で OData として受信できる、SAP Process Integration (PI) に送信されたデータまたは中間ドキュメント (IDOC)。

- 基本認証を使用したデータのコピー。

バージョン 7.0 以降は、SAP ECC のバージョンではなく SAP NetWeaver のバージョンを指します。 たとえば、一般に SAP ECC 6.0 EHP 7 の NetWeaver バージョンは 7.4 以降です。 お使いの環境が不明な場合に、SAP システムからバージョンを確認する手順を次に示します。

1. SAP GUI を使用して SAP システムに接続します。 
2. **[システム]**  ->  **[ステータス]** に移動します。 
3. SAP_BASIS のリリースを調べ、701 以上であることを確認します。  
      ![SAP_BASIS の確認](./media/connector-sap-table/sap-basis.png)

>[!TIP]
>SAP テーブルまたはビューを介して SAP ECC からデータをコピーするには、より高速でスケーラブルな [SAP テーブル](connector-sap-table.md) コネクタを使用します。

## <a name="prerequisites"></a>前提条件

この SAP ECC コネクタを使用するには、SAP ゲートウェイを通して OData サービス経由で SAP ECC エンティティを公開する必要があります。 具体的には次のとおりです。

- **SAP ゲートウェイを設定します**。 バージョン 7.4 より後の SAP NetWeaver があるサーバーでは、SAP ゲートウェイは既にインストールされています。 以前のバージョンの場合、OData サービスを介して SAP ECC データを公開する前に、埋め込み SAP ゲートウェイまたは SAP ゲートウェイ ハブ システムをインストールする必要があります。 SAP ゲートウェイを設定するには、[インストール ガイド](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm)をご覧ください。

- **SAP OData サービスをアクティブ化して構成します**。 OData サービスは TCODE SICF を通じて数秒でアクティブ化できます。 また、どのオブジェクトを公開する必要があるかを構成することもできます。 詳しくは、[ステップ バイ ステップ ガイダンス](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/)に関する記事をご覧ください。

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>はじめに

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、SAP ECC コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

SAP ECC のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| `type` | `type` プロパティは `SapEcc` に設定する必要があります。 | はい |
| `url` | SAP ECC OData サービスの URL。 | はい |
| `username` | SAP ECC への接続に使うユーザー名。 | いいえ |
| `password` | SAP ECC への接続に使うプレーンテキスト パスワード。 | いいえ |
| `connectVia` | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 詳細については、「[前提条件](#prerequisites)」セクションを参照してください。 ランタイムを指定していない場合、既定の Azure 統合ランタイムが使用されます。 | いいえ |

### <a name="example"></a>例

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData URL, e.g., http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of integration runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 次のセクションでは、SAP ECC データセットでサポートされるプロパティの一覧を示します。

SAP ECC からデータをコピーするには、データセットの `type` プロパティを `SapEccResource` に設定します。

次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| `path` | SAP ECC OData エンティティのパス。 | はい |

### <a name="example"></a>例

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path, e.g., dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事をご覧ださい。 次のセクションでは、SAP ECC ソースでサポートされるプロパティの一覧を示します。

### <a name="sap-ecc-as-a-source"></a>ソースとしての SAP ECC

SAP ECC からデータをコピーするには、コピー アクティビティの `source` セクション内の `type` プロパティを `SapEccSource` に設定します。

コピー アクティビティの `source` セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| `type` | コピー アクティビティの `source` セクションの `type` プロパティは `SapEccSource` を設定する必要があります。 | はい |
| `query` | データをフィルター処理するための OData クエリ オプション。 次に例を示します。<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>SAP ECC コネクタは、次の結合された URL からデータをコピーします。<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>詳細については、[OData の URL コンポーネント](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)に関するページを参照してください。 | いいえ |
| `sapDataColumnDelimiter` | 出力データを分けるために SAP RFC に渡される、区切り記号として使用される 1 文字。 | いいえ |
| `httpRequestTimeout` | HTTP 要求が応答を取得する際のタイムアウト (**TimeSpan** 値)。 この値は、応答データの読み取りのタイムアウトではなく、応答の取得のタイムアウトです。 指定しない場合は、既定値の **00:30:00** (30 分) が使用されます。 | いいえ |

### <a name="example"></a>例

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mappings-for-sap-ecc"></a>SAP ECC のデータ型マッピング

SAP ECC からデータをコピーするとき、次の SAP ECC データの OData データ型から Azure Data Factory の中間データ型へのマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| OData のデータ型 | Data Factory の中間データ型 |
|:--- |:--- |
| `Edm.Binary` | `String` |
| `Edm.Boolean` | `Bool` |
| `Edm.Byte` | `String` |
| `Edm.DateTime` | `DateTime` |
| `Edm.Decimal` | `Decimal` |
| `Edm.Double` | `Double` |
| `Edm.Single` | `Single` |
| `Edm.Guid` | `String` |
| `Edm.Int16` | `Int16` |
| `Edm.Int32` | `Int32` |
| `Edm.Int64` | `Int64` |
| `Edm.SByte` | `Int16` |
| `Edm.String` | `String` |
| `Edm.Time` | `TimeSpan` |
| `Edm.DateTimeOffset` | `DateTimeOffset` |

> [!NOTE]
> 複合データ型は現時点ではサポートされていません。

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するページを参照してください。
