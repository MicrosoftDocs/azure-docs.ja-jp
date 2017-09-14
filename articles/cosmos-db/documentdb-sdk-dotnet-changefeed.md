---
title: "Azure DocumentDB .NET Change Feed Processor SDK およびリソース | Microsoft Docs"
description: "リリース日、提供終了日、DocumentDB .NET Change Feed Processor SDK の各バージョン間の変更など、Change Feed Processor API と SDK に関するあらゆる詳細を提供します。"
services: cosmos-db
documentationcenter: .net
author: ealsur
manager: kirillg
editor: mimig1
ms.assetid: f2dd9438-8879-4f74-bb6c-e1efc2cd0157
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2017
ms.author: maquaran
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 239b590a1e3a83fe0205dd8169697db745d7f75e
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---
# <a name="documentdb-net-change-feed-processor-sdk-download-and-release-notes"></a>DocumentDB .NET Change Feed Processor SDK: ダウンロードおよびリリース ノート
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [.NET Change Feed](documentdb-sdk-dotnet-changefeed.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.JS](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST ()](https://docs.microsoft.com/rest/api/documentdb/)
> * [REST リソース プロバイダー](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

<table>

<tr><td>**SDK のダウンロード**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)</td></tr>

<tr><td>**API ドキュメント**</td><td>[Change Feed Processor ライブラリ API リファレンス ドキュメント](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)</td></tr>

<tr><td>**概要**</td><td>[DocumentDB Change Feed Processor .NET SDK の概要](change-feed.md)</td></tr>

<tr><td>**現在サポートされているフレームワーク**</td><td>[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## <a name="release-notes"></a>リリース ノート

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* 変更フィードが空であるか保留中の作業がない場合の、残っている作業の推定量の計算に関する問題を修正しました。
* [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) バージョン 1.13.2 以降と互換性があります。

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Change Feed に処理が残っている作業の推定量を取得するメソッドが追加されました。
* [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) バージョン 1.13.2 以降と互換性があります。

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK
* [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) バージョン 1.14.1 以下と互換性があります。

## <a name="release--retirement-dates"></a>リリース日と提供終了日
Microsoft は、新しい/サポートされるバージョンに速やかに移行する目的で、SDK の提供終了を少なくともその **12 か月** 前に通知します。

新しい機能と最適化は現在の SDK にのみ追加されます。そのため、常に可能な限り最新の SDK バージョンにアップグレードすることが推奨されます。 

提供終了の SDK を使用した Cosmos DB への要求は、サービスによって拒否されます。

<br/>

| バージョン | リリース日 | 提供終了日 |
| --- | --- | --- |
| [1.1.1](#1.1.1) |2017 年 8 月 29 日 |--- |
| [1.1.0](#1.1.0) |2017 年 8 月 13 日 |--- |
| [1.0.0](#1.0.0) |2017 年 7 月 7 日 |--- |


## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>関連項目
Cosmos DB の詳細については、[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) サービス ページをご覧ください。 


