---
title: Azure Germany データベース サービス | Microsoft Docs
description: Azure Germany のデータベース サービスを比較します
services: germany
cloud: na
documentationcenter: na
author: gitralf
manager: rainerst
ms.assetid: na
ms.service: germany
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2017
ms.author: ralfwi
ms.openlocfilehash: 45e058d887ae79164196ab310f93270accbfb389
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59784357"
---
# <a name="azure-germany-database-services"></a>Azure Germany データベース サービス
## <a name="sql-database"></a>SQL Database
Azure SQL Database V12 は、Azure Germany で一般提供されています。 メタデータの可視性構成と保護のベスト プラクティスのガイダンスについては、[SQL Server データベース エンジンの Microsoft セキュリティ センター](/sql/relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database)に関するページと [SQL Database グローバル ドキュメント](../sql-database/index.yml)に関するページを参照してください。

### <a name="variations"></a>バリエーション
Azure Germany での SQL Database のアドレスは、グローバル Azure でのアドレスと異なります。

| サービスの種類 | グローバル Azure | Azure Germany |
| --- | --- | --- |
| SQL Database | *.database.windows.net | *.database.cloudapi.de |


## <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Azure Cache for Redis の詳細とその使用方法については、[Azure Cache for Redis グローバル ドキュメント](../azure-cache-for-redis/index.md)に関するページを参照してください。

### <a name="variations"></a>バリエーション
Azure Germany での Azure Cache for Redis のアクセスと管理を行うための URL は、グローバル Azure での URL と異なります。

| サービスの種類 | グローバル Azure | Azure Germany |
| --- | --- | --- |
| キャッシュのエンドポイント | *.redis.cache.windows.net | *.redis.cache.cloudapi.de |
| Azure ポータル | https://portal.azure.com | https://portal.microsoftazure.de |

> [!NOTE]
> スクリプトやコードでは必ず、適切なエンドポイントと環境を考慮する必要があります。 詳細については、「[Azure PowerShell を使用した Azure Cache for Redis の管理](../azure-cache-for-redis/cache-howto-manage-redis-cache-powershell.md)」の「Microsoft Azure Germany に接続するには」を参照してください。
>
>


## <a name="next-steps"></a>次の手順
補足情報と更新情報については、[Azure Germany のブログ](https://blogs.msdn.microsoft.com/azuregermany/)を参照してください。
