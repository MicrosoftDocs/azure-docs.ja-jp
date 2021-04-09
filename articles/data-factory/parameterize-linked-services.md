---
title: Azure Data Factory のリンクされたサービスのパラメーター化
description: Azure Data Factory のリンクされたサービスをパラメーター化し、実行時に動的な値を渡す方法について説明します。
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/18/2021
author: dcstwh
ms.author: weetok
ms.openlocfilehash: df26b77f37100ae41b26c013c57cccbfa0d7e205
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595585"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Azure Data Factory のリンクされたサービスのパラメーター化

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

リンクされたサービスをパラメーター化し、実行時に動的な値を渡せるようになりました。 たとえば、同じ論理 SQL サーバー上の異なるデータベースに接続する場合に、リンクされたサービスの定義内でデータベース名をパラメーター化することができるようになりました。 これにより、論理 SQL サーバー上のデータベースごとに、リンクされたサービスを作成する必要がなくなります。 リンクされたサービスの定義内で、他のプロパティをパラメーター化することもできます (たとえば、*ユーザー名* など)。

リンクされたサービスをパラメーター化するには、Azure portal の Data Factory UI か、プログラミング インターフェイスを使用できます。

> [!TIP]
> パスワードやシークレットはパラメーター化しないようにすることをお勧めします。 接続文字列はすべて Azure Key Vault 内に格納し、*シークレット名* をパラメーター化するようにしてください。

> [!Note]
> パラメーター名に "-" を使用する未解決のバグがあります。バグが解決されるまでは、"-" を含まない名前を使用することをお勧めします。

この機能の概要とデモンストレーションについては、以下の 7 分間の動画を視聴してください。

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-linked-service-types"></a>サポートされるリンクされたサービスの種類

任意の種類のリンクされたサービスをパラメーター化できます。
リンクされたサービスを UI で作成する場合、Data Factory は、次の種類のリンクされたサービスに対して組み込みのパラメーター化エクスペリエンスを提供します。 リンクされたサービスの作成/編集ブレードで、新しいパラメーターのオプションを検索し、動的なコンテンツを追加できます。

- Amazon Redshift
- Amazon S3
- Azure Blob Storage
- Azure Cosmos DB (SQL API)
- Azure Data Lake Storage Gen2
- Azure Database for MySQL
- Azure Databricks
- Azure Key Vault
- Azure SQL Database 
- Azure SQL Managed Instance
- Azure Synapse Analytics 
- Azure Table Storage
- 汎用 HTTP
- 汎用 REST
- MySQL
- Oracle
- SQL Server

上記の一覧にないその他のリンクされたサービスの種類では、UI で JSON を編集して、リンクされたサービスをパラメーター化できます。

- リンクされたサービスの作成/編集ブレードで > 下部にある "詳細" を展開し > "JSON 形式で動的な内容を指定する" ためのチェックボックスをオンにして > リンクされたサービスの JSON ペイロードを指定します。 
- または、パラメーター化せずにリンクされたサービスを作成した後で、[管理ハブ](author-visually.md#management-hub) > リンクされたサービス > 特定のリンクされたサービスを検索 > "コード" (ボタン {}) をクリックして JSON を編集します。 

` parameters` セクションを追加してパラメーターを定義し、` @{linkedService().paraName} ` を使用してパラメーターを参照するには、[JSON サンプル](#json)を参照してください。

## <a name="data-factory-ui"></a>Data Factory UI

![リンクされたサービスの定義に動的なコンテンツを追加する](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![新しいパラメーターを作成する](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
