---
title: マネージド ID を使用してデータ ソースへの接続を設定する
titleSuffix: Azure Cognitive Search
description: マネージド ID を使用してデータ ソースへのインデクサー接続を設定する方法を説明します
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 95f1c4bf9b599da8285ac69e299549e5aa73c2f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92519590"
---
# <a name="set-up-an-indexer-connection-to-a-data-source-using-a-managed-identity"></a>マネージド ID を使用してデータ ソースへのインデクサー接続を設定する

> [!IMPORTANT] 
> 無料の Azure Cognitive Search レベルの場合、マネージド ID でデータ ソースへの接続を設定することはできません。

Azure Cognitive Search の[インデクサー](search-indexer-overview.md)は、データ ソースから Azure Cognitive Search にデータをプルするためのクローラーです。 インデクサーは、ユーザーが作成したデータ ソース オブジェクトからデータ ソース接続を取得します。 データ ソース オブジェクトには、通常、対象のデータ ソースの資格情報が含まれています。 たとえば、BLOB ストレージ コンテナーのデータに対してインデックスを作成する場合は、データ ソース オブジェクトに Azure Storage アカウント キーを含めることができます。

多くの場合、データ ソース オブジェクトで資格情報を直接指定することに問題はありませんが、いくつかの課題もあります。
* データ ソース オブジェクトを作成するコード内で、資格情報のセキュリティを確保するにはどうすればよいか。
* アカウント キーまたはパスワードが侵害され、パスワードを変更する必要がある場合には、インデクサーがデータ ソースに再び接続できるように、新しいアカウント キーやパスワードでデータ ソース オブジェクトを更新する必要がある。

これらの問題は、マネージド ID を使用して接続を設定することにより解決できます。

## <a name="using-managed-identities"></a>マネージド ID の使用

[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) は、Azure Active Directory (Azure AD) で自動管理される ID を Azure サービスで使用できるようにする機能です。 Azure Cognitive Search でこの機能を使用すると、資格情報が含まれていない接続文字列を使用してデータ ソース オブジェクトを作成できます。 その場合、検索サービスには、Azure ロールベースのアクセス制御 (Azure RBAC) によってデータ ソースへのアクセスが許可されます。

マネージド ID を使用してデータ ソースを設定することで、データ ソースの資格情報を変更しても、インデクサーでは引き続きデータソースに接続できるようになります。 また、アカウント キーを含めたり、Key Vault を使用してアカウント キーを取得したりしなくても、コード内にデータ ソース オブジェクトを作成することができます。

## <a name="limitations"></a>制限事項

マネージド ID を使用したインデクサー接続の設定は、次のデータ ソースでサポートされています。 

* [Azure BLOB ストレージ、Azure Data Lake Storage Gen2 (プレビュー)、Azure Table Storage](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Azure SQL Database](search-howto-managed-identities-sql.md)

次の機能では、現在のところ、マネージド ID した接続の設定はサポートされていません。
* ナレッジ ストア
* カスタム スキル
 
## <a name="next-steps"></a>次のステップ

マネージド ID を使用してインデクサー接続を設定する方法の詳細については、次を参照してください。

* [Azure BLOB ストレージ、Azure Data Lake Storage Gen2 (プレビュー)、Azure Table Storage](search-howto-managed-identities-storage.md)
* [Azure Cosmos DB](search-howto-managed-identities-cosmos-db.md)
* [Azure SQL Database](search-howto-managed-identities-sql.md)