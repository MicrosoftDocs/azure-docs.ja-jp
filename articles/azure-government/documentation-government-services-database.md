---
title: "Azure Government のデータベース | Microsoft Docs"
description: "Azure Government アプリケーションの機能の比較と開発におけるガイダンスを示します。"
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: a1e173a9-996a-4091-a2e3-6b1e36da9ae1
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/14/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: c94e643565374f852633254a94c2138713cc290e
ms.openlocfilehash: 58d6295057c71dad488e04be25cc9a85d73bcd20


---
# <a name="azure-government-databases"></a>Azure Government データベース
## <a name="sql-database"></a>SQL Database
メタデータの可視性の構成に関する詳しい指針と、保護に関するベスト プラクティスについては、<a href="https://msdn.microsoft.com/en-us/library/bb510589.aspx">SQL Server データベース エンジンのセキュリティ センター</a>と [Azure SQL Database のパブリック ドキュメント](../sql-database/index.md)を参照してください。

### <a name="variations"></a>バリエーション
SQL V12 Database は、Azure Government で一般提供されています。

Azure Government では、SQL Azure サーバーのアドレスが異なります。

| サービスの種類 | Azure Public | Azure Government |
| --- | --- | --- |
| SQL Database |*.database.windows.net |*.database.usgovcloudapi.net |

### <a name="considerations"></a>考慮事項
以下の情報は、Azure SQL に関する Azure Government の機能領域について記述したものです。

| 許可される規制対象データ | 許可されない規制対象データ |
| --- | --- |
| Microsoft Azure SQL で保存されて処理されるすべてのデータには、Azure Government の規制対象データが含まれていてもかまいません。 Azure Government の規制対象データの転送については、データベース ツールを使用する必要があります。 |Azure SQL のメタデータに、輸出規制対象データを含めることは許可されません。 このメタデータには、ストレージ製品を作成したり管理したりする際に入力するあらゆる構成データが含まれます。  データベース名、サブスクリプション名、リソース グループ、サーバー名、サーバー管理者ログイン、デプロイ名、リソース名、リソース タグの各フィールドには規制対象データを入力しないでください。 |

## <a name="azure-redis-cache"></a>Azure Redis Cache
このサービスとその使用方法について詳しくは、[Azure Redis Cache のパブリック ドキュメント](../redis-cache/index.md)をご覧ください。

### <a name="variations"></a>バリエーション
Azure Government では、Azure Redis Cache に対するアクセスと管理に使用する URL が異なります。

| サービスの種類 | Azure Public | Azure Government |
| --- | --- | --- |
| キャッシュのエンドポイント |*.redis.cache.windows.net |*. redis.cache.usgovcloudapi.net |
| Azure ポータル |https://portal.azure.com |https://portal.azure.us |

> [!NOTE]
> スクリプトやコードでは必ず、適切なエンドポイントと環境を考慮する必要があります。 詳細については、[Azure Government Cloud に接続する方法](../redis-cache/cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-azure-government-cloud-or-azure-china-cloud)に関するページを参照してください。
> 
> 

### <a name="considerations"></a>考慮事項
以下の情報は、Azure Redis Cache に関する Azure Government の機能領域について記述したものです。

| 許可される規制対象データ | 許可されない規制対象データ |
| --- | --- |
| Azure Redis Cache で保存および処理されるデータには、Azure Government の規制対象データが含まれていてもかまいません。 |Azure Redis Cache のメタデータに輸出規制対象データを含めることは、許可されていません。 キャッシュ名、VNET 名、サブスクリプション名、リソース グループ、リソース タグ、Redis プロパティの各フィールドには、規制対象データを入力しないでください。 |

## <a name="next-steps"></a>次のステップ
補足情報と最新情報については、<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government ブログ</a>を講読してください。




<!--HONumber=Nov16_HO3-->


