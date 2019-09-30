---
title: Azure Data Factory のリンクされたサービス |Microsoft Docs
description: Data Factory にリンクされたサービスについて説明します。 リンクされたサービスは、計算/データ ストアをデータ ファクトリにリンクします。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: 80e9cee0d973dc8575e9645c537b6b69fbeef700
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70137117"
---
# <a name="linked-services-in-azure-data-factory"></a>Azure Data Factory のリンクされたサービス
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください。"]
> * [Version 1](v1/data-factory-create-datasets.md)
> * [現在のバージョン](concepts-datasets-linked-services.md)

この記事では、リンクされたサービスとは何か、それらはどのように JSON 形式で定義され、Azure Data Factory パイプラインでどのように使用されるかを説明します。

Azure Data Factory を初めて使用する場合は、[Azure Data Factory の概要](introduction.md)に関するページを参照してください。

## <a name="overview"></a>概要
データ ファクトリは、1 つまたは複数のパイプラインを持つことができます。 **パイプライン**は、1 つのタスクを連携して実行する**アクティビティ**の論理的なグループです。 パイプライン内の複数のアクティビティは、データに対して実行するアクションを定義します。 たとえば、コピー アクティビティを使用して、オンプレミスの SQL Server から Azure Blob Storage にデータをコピーすることができます。 その後、Azure HDInsight クラスターで Hive スクリプトを実行する Hive アクティビティを使用して、Blob Storage のデータを処理し、出力データを生成できます。 最後に、別のコピー アクティビティを使用して、ビジネス インテリジェンス (BI) レポート ソリューションが構築されている Azure SQL Data Warehouse に出力データをコピーできます。 パイプラインとアクティビティの詳細については、Azure Data Factory の[パイプラインとアクティビティ](concepts-pipelines-activities.md)を参照してください。

ここで、**データセット**とは、**アクティビティ**で入力と出力として使用するデータを単に指定または参照するデータの名前付きビューです。

データセットを作成する前に、**リンクされたサービス**を作成して、データ ストアとデータ ファクトリをリンクする必要があります。 リンクされたサービスは、接続文字列によく似ており、Data Factory が外部リソースに接続するために必要な接続情報を定義します。 データセットはリンクされたデータ ストア内のデータの構造を表すもので、リンクされたサービスはデータ ソースへの接続を定義するもの、と捉えることができます。 たとえば、Azure Storage のリンクされたサービスは、ストレージ アカウントをデータ ファクトリにリンクします。 Azure Blob データセットは、処理対象の入力 BLOB を含む Azure ストレージ アカウント内の BLOB コンテナーとフォルダーを表します。

シナリオの例を次に示します。 Blob Storage のデータを Azure SQL Database にコピーするために、2 つのリンクされたサービスを作成します Azure Storage、Azure SQL Database)。 次に、2 つのデータセットを作成します。Azure Blob データセット (Azure Storage リンクされたサービスを参照するデータセット) と、Azure SQL Table データセット (Azure SQL Database リンクされたサービスを参照するデータセット) です。 Azure Storage と Azure SQL Database の各リンクされたサービスに含まれる接続文字列を、Data Factory が実行時に使用して、Azure Storage と Azure SQL Database それぞれに接続します。 Azure Blob データセットは、Blob Storage 内の入力 BLOB が含まれた BLOB コンテナーと BLOB フォルダーを示しています。 Azure SQL Table データセットは、データのコピー先である SQL Database 内の SQL テーブルを示しています。

次の図は、Data Factory でのパイプライン、アクティビティ、データセット、リンクされたサービスの関係を示しています。

![パイプライン、アクティビティ、データセット、リンクされたサービスの関係](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>リンクされたサービスの JSON
Data Factory のリンクされたサービスは、次のように JSON 形式で定義されます。

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

次の表では、上記の JSON のプロパティについて説明します。

プロパティ | 説明 | 必須 |
-------- | ----------- | -------- |
名前 | リンクされたサービスの名前。 [Azure Data Factory - 名前付け規則](naming-rules.md)を参照してください。 |  はい |
type | リンクされたサービスの種類 例: AzureStorage (データ ストア) または AzureBatch (コンピューティング)。 typeProperties の説明を参照してください。 | はい |
typeProperties | 型のプロパティは、データ ストアまたはコンピューティングごとに異なります。 <br/><br/> サポートされているデータ ストア型と型のプロパティについては、この記事の「[データセットの型](concepts-datasets-linked-services.md#dataset-type)」の表を参照してください。 データ ストアに固有の型のプロパティについては、データ ストア コネクタに関する記事に移動してください。 <br/><br/> サポートされているコンピューティング型とその型のプロパティについては、[コンピューティングのリンクされたサービス](compute-linked-services.md)に関する記事を参照してください。 | はい |
connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 Azure 統合ランタイムまたは自己ホスト型統合ランタイムを使用できます (データ ストアがプライベート ネットワークにある場合)。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 | いいえ

## <a name="linked-service-example"></a>リンクされたサービスの例
次のリンクされたサービスは、Azure Storage のリンクされたサービスです。 type が AzureStorage に設定されている点に注目してください。 Azure Storage のリンクされたサービスの型のプロパティには、接続文字列が含まれます。 Data Factory サービスは、この接続文字列を使用して、実行時にデータ ストアに接続します。

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="create-linked-services"></a>リンクされたサービスを作成します

リンクされたサービスは、以下のツールや SDK のいずれかを使用して作成できます。[.NET API](quickstart-create-data-factory-dot-net.md)、[PowerShell](quickstart-create-data-factory-powershell.md)、[REST API](quickstart-create-data-factory-rest-api.md)、Azure Resource Manager テンプレート、Azure portal

## <a name="data-store-linked-services"></a>データ ストアのリンクされたサービス
Data Factory によってサポートされているデータ ストアの一覧は、[コネクタの概要](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事をご覧ください。 サポートされている接続プロパティを確認するには、データ ストアをクリックしてください。

## <a name="compute-linked-services"></a>コンピューティングのリンクされたサービス
データ ファクトリから接続できるさまざまなコンピューティング環境とさまざまな構成の詳細については、[サポートされるコンピューティング環境](compute-linked-services.md)を参照してください。

## <a name="next-steps"></a>次の手順
これらのツールや SDK のいずれかを使用してパイプラインとデータセットを作成する詳しい手順については、次のチュートリアルを参照してください。

- [クイック スタート: .NET を使用してデータ ファクトリを作成する](quickstart-create-data-factory-dot-net.md)
- [クイック スタート: PowerShell を使用してデータ ファクトリを作成する](quickstart-create-data-factory-powershell.md)
- [クイック スタート: REST API を使用してデータ ファクトリを作成する](quickstart-create-data-factory-rest-api.md)
- [クイック スタート: Azure portal を使用したデータ ファクトリの作成](quickstart-create-data-factory-portal.md)
