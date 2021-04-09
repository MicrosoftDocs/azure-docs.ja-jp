---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 91a52e7eac40c0ac2ab682f251a2ae0013259b25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "77013779"
---
## <a name="what-is-table-storage"></a>Table Storage とは
Azure Table Storage は、大量の構造化データを格納します。 このサービスは、Azure クラウドの内部および外部からの認証された呼び出しを受け付ける NoSQL データストアです。 Azure のテーブルは、構造化された非リレーショナル データを格納するのに最適です。 Table Storage の一般的な用途には、次のようなものがあります。

* Web スケール アプリケーションにサービスを提供できる数テラバイトの構造化データを格納する
* 複雑な結合、外部キー、またはストアド プロシージャを必要とせず、高速アクセスのために非正規化できるデータセットを格納する
* クラスター化インデックスを使用して高速なデータのクエリを実行する
* OData プロトコルおよび LINQ クエリを WCF Data Service .NET ライブラリと共に使用してデータにアクセスする

Table Storage を使用して、構造化された非リレーショナル データの膨大なセットを格納してクエリを実行することができ、テーブルはデータ量が増加すると拡張されます。

## <a name="table-storage-concepts"></a>Table Storage の概念
Table Storage には次の構成要素があります。

![Table Storage のコンポーネントの図][Table1]

* **URL 形式:** Azure Table Storage アカウントでは、以下の形式を使用します。`http://<storage account>.table.core.windows.net/<table>`

  Azure Cosmos DB のテーブル API アカウントでは、以下の形式を使用します。`http://<storage account>.table.cosmosdb.azure.com/<table>`  

  このアドレスを OData プロトコルで使用して、Azure テーブルを直接アドレス指定できます。 詳細については、[OData.org][OData.org] の Web サイトを参照してください。
* **アカウント:** Azure Storage へのアクセスはすべて、ストレージ アカウント経由で行います。 ストレージ アカウントの詳細については、「[ストレージ アカウントの概要](../articles/storage/common/storage-account-overview.md)」を参照してください。

    Azure Cosmos DB へのアクセスはすべて、テーブル API アカウント経由で行います。 テーブル API アカウントの作成の詳細については、[テーブル API アカウントの作成](../articles/cosmos-db/create-table-dotnet.md#create-a-database-account)に関するページをご覧ください。
* **テーブル:** テーブルは、エンティティのコレクションです。 テーブルではエンティティにスキーマを設定しないため、1 つのテーブルに異なるプロパティのセットを持つエンティティが含まれている場合があります。  
* **エンティティ:** エンティティは、プロパティのセットで、データベースの行に似ています。 Azure Storage 内のエンティティには、最大 1 MB のサイズを指定できます。 Azure Cosmos DB 内のエンティティには、最大 2 MB のサイズを指定できます。
* **プロパティ:** プロパティは、名前と値のペアです。 それぞれのエンティティは、データを格納するために最大で 252 個のプロパティを含むことができます。 さらに、それぞれのエンティティは、パーティション キー、行キー、およびタイムスタンプを指定する、3 つのシステム プロパティを持ちます。 同じパーティション キーを持つエンティティは、アトミック操作でより迅速な照会と挿入/更新が可能です。 エンティティの行キーは、パーティション内の一意の識別子です。

テーブルとプロパティの名前付け規則については、「 [Understanding the Table Service Data Model (Table サービス データ モデルの概要)](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model)」を参照してください。

[Table1]: ./media/storage-table-concepts-include/table1.png
[OData.org]: http://www.odata.org/
