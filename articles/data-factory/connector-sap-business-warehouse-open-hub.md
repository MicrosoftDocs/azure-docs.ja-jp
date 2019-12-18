---
title: オープン ハブを介して SAP Business Warehouse からデータをコピーする
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、オープン ハブを介して SAP Business Warehouse (BW) から、サポートされているシンク データ ストアへデータをコピーする方法について説明します。
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: 84098901d58e2087c7ece77049e445bb5c76f2a9
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74923792"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Azure Data Factory を使用するオープン ハブを介して SAP Business Warehouse からデータをコピーする

この記事では、Azure Data Factory のコピー アクティビティを使用して、オープン ハブを介して SAP Business Warehouse (BW) からデータをコピーする方法の概要を説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

>[!TIP]
>SAP データ統合シナリオにおける ADF の全体的なサポートについては、[「Azure Data Factory を使用した SAP データの統合」ホワイトペーパー](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf)の詳細手順、比較、およびガイダンスを参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

オープン ハブ コネクタ経由のこの SAP Business Warehouse は、次のアクティビティでサポートされています。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

SAP Business Warehouse のデータは、オープン ハブを介して、サポートされている任意のシンク データ ストアにコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この SAP Business Warehouse オープン ハブ コネクタは以下をサポートします。

- SAP Business Warehouse **バージョン 7.01 以上 (2015 年以後にリリースされた最近の SAP サポート パッケージ スタックの場合)** 。
- オープン ハブ宛先の、DSO、InfoCube、MultiProvider、DataSource などがその下にある可能性があるローカル テーブルを介したデータのコピー。
- 基本認証を使用したデータのコピー。
- アプリケーション サーバーへの接続。

## <a name="sap-bw-open-hub-integration"></a>SAP BW オープン ハブの統合 

[SAP BW オープン ハブ サービス](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service)は、SAP BW からデータを抽出する効率的な方法です。 次の図は、お客様が利用する SAP システム内の典型的なフローの 1 つを示しています。この場合、データは SAP ECC、PSA、DSO、キューブと流れています。

SAP BW オープン ハブ宛先 (OHD) は、SAP データの中継先のターゲットを定義します。 SAP データ転送プロセス (DTP) によってサポートされているすべてのオブジェクトは、たとえば DSO、InfoCube、DataSource などのオープン ハブ データ ソースとして使用できます。中継されたデータが保存されるオープン ハブ宛先の種類は、データベース テーブル (ローカルまたはリモート) またはフラット ファイルである場合があります。 この SAP BW オープン ハブ コネクタは、BW の OHD ローカル テーブルからのデータ コピーをサポートしています。 その他の種類を使用している場合は、その他のコネクタを使用してデータベースまたはファイル システムに直接接続できます。

![SAP BW オープン ハブ](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>差分抽出フロー

ADF SAP BW オープン ハブ コネクタでは、次の 2 つの省略可能なプロパティが提供されます。オープン ハブからの差分読み込みを処理するために使用できる `excludeLastRequest` と `baseRequestId` です。 

- **excludeLastRequestId**: 最後の要求のレコードを除外するかどうか。 既定値は true です。 
- **baseRequestId**: 差分読み込み要求の ID。 設定されると、requestId がこのプロパティの値より大きいデータのみが取得されます。 

全体として、SAP InfoProviders から Azure Data Factory (ADF) への実行は、次の 2 つの手順で構成されます。 

1. **SAP BW データ転送プロセス (DTP)** この手順では、SAP BW InfoProvider から SAP BW オープン ハブ テーブルにデータがコピーされます 

1. **ADF データのコピー** この手順では、オープン ハブ テーブルが ADF コネクタによって読み取られます 

![差分抽出フロー](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

最初の手順では、DTP が実行されます。 実行ごとに新しい SAP 要求 ID が作成されます。 要求 ID はオープン ハブ テーブルに保存され、差分を識別するために ADF コネクタによって使用されます。 2 つ目の手順は非同期で実行されます。DTP は SAP によってトリガーされ、ADF データのコピーは ADF を介してトリガーされます。 

既定では、ADF でオープン ハブ テーブルから最後の差分を読み取ることはありません ("最後の要求を除外する" オプションは true です)。 これによって、ADF のデータはオープン ハブ テーブルのデータに関して 100% 最新ではありません (最後の差分はありません)。 代わりに、このプロシージャによって、非同期の抽出で行を失わないようにすることができます。 ADF でオープン ハブ テーブルを読み取るときでも問題なく動作し、DTP は引き続き同じテーブルに書き込まれます。 

通常、ADF によって最後の実行のコピーされた最大の要求 ID をステージング データ ストア (上述のダイアグラムの Azure BLOB など) に保存します。 そのため、後続の実行で ADF による 2 回目の同じ要求が読み取られることはありません。 その間、データはオープン ハブ テーブルから自動的に削除されないことに注意してください。

適切は差分処理では、同じオープン ハブ テーブルの異なる DTP からの要求 ID を持つことは許可されません。 そのため、オープン ハブ宛先 (OHD) ごとに複数の DTP を作成する必要はありません。 同じ InfoProvider から完全抽出と差分抽出が必要な場合、同じ InfoProvider に OHD を 2 つ作成する必要があります。 

## <a name="prerequisites"></a>前提条件

この SAP Business Warehouse オープン ハブ コネクタを使用するには、次の作業を行う必要があります。

- バージョン 3.13 以上のセルフホステッド統合ランタイムをセットアップする。 詳細については、[セルフホステッド統合ランタイム](create-self-hosted-integration-runtime.md)に関する記事をご覧ください。

- SAP の Web サイトから **64 ビットの [SAP .NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html)** をダウンロードし、それをセルフホステッド IR マシンにインストールします。 インストール時には、省略可能なセットアップ手順のウィンドウで、次の図のように **[Install Assemblies to GAC] (アセンブリを GAC にインストールする)** オプションを選択したことを確認します。 

    ![SAP .NET Connector をインストールする](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Data Factory の BW コネクタで使用する予定の SAP ユーザーは、以下のアクセス許可を持っている必要があります。 

    - RFC と SAP BW の認可。 
    - 認可オブジェクト "S_SDSAUTH" の "実行" アクティビティに対するアクセス許可。

- SAP オープン ハブ宛先の種類は、[Technical Key] (テクニカル キー) オプションをオンにして、**データベース テーブル**として作成します。  また、必須ではありませんが、[Deleting Data from Table] (テーブルからデータを削除する) はオンにしないままにすることをお勧めします。 DTP を利用して (直接実行するか、既存のプロセス チェーンに統合する)、選択したソース オブジェクト (キューブなど) のデータをオープン ハブ宛先テーブルに取り込みます。

## <a name="getting-started"></a>使用の開始

> [!TIP]
>
> SAP BW オープン ハブ コネクタの使用に関するチュートリアルについては、「[Load data from SAP Business Warehouse (BW) by using Azure Data Factory](load-sap-bw-data.md)」 (Azure Data Factory を使用して SAP Business Warehouse (BW) からデータを読み込む) を参照してください。

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下のセクションでは、SAP Business Warehouse オープン ハブ コネクタに固有の Data Factory エンティティを定義するために使用されるプロパティの詳細を説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

SAP Business Warehouse オープン ハブのリンクされたサービスでは、以下のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**SapOpenHub** | はい |
| server | SAP BW インスタンスが存在するサーバーの名前。 | はい |
| systemNumber | SAP BW システムのシステムの数。<br/>使用できる値: 文字列として表される 2 桁の 10 進数。 | はい |
| clientId | SAP BW システム内のクライアントのクライアント ID。<br/>使用できる値: 文字列として表される 3 桁の 10 進数。 | はい |
| language | SAP システムで使用する言語。 | いいえ (既定値は **EN**)|
| userName | SAP サーバーにアクセスできるユーザーの名前。 | はい |
| password | ユーザーのパスワード。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 「[前提条件](#prerequisites)」に記されているように、セルフホステッド統合ランタイムが必要です。 |はい |

**例:**

```json
{
    "name": "SapBwOpenHubLinkedService",
    "properties": {
        "type": "SapOpenHub",
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

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、SAP BW オープン ハブ データセットでサポートされるプロパティの一覧を示します。

SAP BW オープン ハブとの間でデータをコピーするには、データセットの type プロパティを **SapOpenHubTable** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **SapOpenHubTable** に設定する必要があります。  | はい |
| openHubDestinationName | データのコピー元になるオープン ハブ宛先の名前。 | はい |

データセットに `excludeLastRequest` と `baseRequestId` を設定していた場合は現状のまま引き続きサポートされますが、今後のアクティビティ ソースでは新しいモデルを使用することをお勧めします。

**例:**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、SAP BW オープン ハブ ソースでサポートされるプロパティの一覧を示します。

### <a name="sap-bw-open-hub-as-source"></a>ソースとしての SAP BW オープン ハブ

SAP BW Open Hub からデータをコピーするために、コピー アクティビティの **source** セクションでは次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの **type** プロパティは **SapOpenHubSource** に設定する必要があります | はい |
| excludeLastRequest | 最後の要求のレコードを除外するかどうか。 | いいえ (既定値は **true**)。 |
| baseRequestId | 差分読み込み要求の ID。 設定されると、requestId がこのプロパティの値**より大きい**データのみが取得されます。  | いいえ |

>[!TIP]
>オープン ハブ テーブルには、1 つの要求 ID によって生成されたデータのみが含まれています。たとえば、常に完全な読み込みを行い、テーブル内の既存のデータを上書きする場合や、DTP はテストのために 1 回のみ実行する場合は、データを外へコピーするため、忘れずに "excludeLastRequest" オプションをオフにします。

データ読み込みを高速化するために、コピー アクティビティに [`parallelCopies`](copy-activity-performance.md#parallel-copy) を設定して、SAP BW オープン ハブから並行してデータを読み込むことができます。 たとえば、`parallelCopies` を 4 に設定すると、Data Factory では同時に 4 つの RFC 呼び出しが実行され、各 RFC 呼び出しは、DTP 要求 ID とパッケージ ID でパーティション分割された SAP BW オープン ハブ テーブルからデータの一部を取得します。 これは、一意の DTP 要求 ID + パッケージ ID の数値が `parallelCopies` の値よりも大きい場合に適用されます。 ファイルベースのデータ ストアにデータをコピーする場合は、複数のファイルとしてフォルダーに書き込む (フォルダー名のみを指定する) こともお勧めします。この場合、1 つのファイルに書き込むよりもパフォーマンスが優れています。

**例:**

```json
"activities":[
    {
        "name": "CopyFromSAPBWOpenHub",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW Open Hub input dataset name>",
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
                "type": "SapOpenHubSource",
                "excludeLastRequest": true
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>SAP BW オープン ハブのデータ型マッピング

SAP BW オープン ハブからデータをコピーするときには、以下に示す、SAP BW のデータ型から Azure Data Factory の中間データ型へのマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| SAP ABAP の型 | Data Factory の中間データ型 |
|:--- |:--- |
| C (String) | string |
| I (integer) | Int32 |
| F (Float) | Double |
| D (Date) | string |
| T (Time) | string |
| P (BCD Packed、Currency、Decimal、Qty) | Decimal |
| N (Numc) | string |
| X (Binary および Raw) | string |

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[ルックアップ アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。


## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
