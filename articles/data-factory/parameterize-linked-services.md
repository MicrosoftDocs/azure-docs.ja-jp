---
title: Azure Data Factory のリンクされたサービスのパラメーター化 | Microsoft Docs
description: Azure Data Factory のリンクされたサービスをパラメーター化し、実行時に動的な値を渡す方法について説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/18/2018
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: 0239c53f98fba201b6d70e1e2212eea36134e30d
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57574629"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Azure Data Factory のリンクされたサービスのパラメーター化

リンクされたサービスをパラメーター化し、実行時に動的な値を渡せるようになりました。 たとえば、同じ Azure SQL Database サーバー上の異なるデータベースに接続する場合に、リンクされたサービスの定義内でデータベース名をパラメーター化することができます。 これにより、Azure SQL Database サーバー上のデータベースごとに、リンクされたサービスを作成する必要がなくなります。 リンクされたサービスの定義内で、他のプロパティをパラメーター化することもできます (たとえば、*ユーザー名*など)。

リンクされたサービスをパラメーター化するには、Azure Portal の Data Factory UI か、プログラミング インターフェイスを使用できます。

> [!TIP]
> パスワードやシークレットはパラメーター化しないようにすることをお勧めします。 接続文字列はすべて Azure Key Vault 内に格納し、*シークレット名*をパラメーター化するようにしてください。

この機能の概要とデモンストレーションについては、以下の 7 分間の動画を視聴してください。

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>サポートされているデータ ストア

現在のところ、リンクされたサービスのパラメーター化は、次のデータ ストアを対象に、Azure portal の Data Factory UI でサポートされています。 その他のすべてのデータ ストアについては、**[接続]** タブで **[コード]** アイコンを選択し、JSON エディターを使用することで、リンクされたサービスをパラメーター化できます。
- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server
- Oracle
- Cosmos DB
- Amazon Redshift
- MySQL
- Azure Database for MySQL

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
            "connectionString": {
                "value": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
                "type": "SecureString"
            }
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
