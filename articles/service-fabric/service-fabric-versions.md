---
title: Azure Service Fabric クラスターのバージョンについて | Microsoft Docs
description: サポートされている Azure Service Fabric クラスターのバージョン
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/24/2018
ms.author: aljo
ms.openlocfilehash: de5522e68d1329ce2b80a4d3c7045d38c13169e5
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57191649"
---
# <a name="supported-service-fabric-versions"></a>サポートされている Service Fabric のバージョン

サポートされている Service Fabric のバージョンが常にクラスターで実行されているようにします。 Microsoft が Service Fabric の新バージョン リリースをアナウンスした日から最短で 60 日後には、以前のバージョンがサポート期間の終了として指定されます。 新バージョンのリリースは、[Service Fabric チーム ブログ](https://blogs.msdn.microsoft.com/azureservicefabric/)でお知らせします。

サポートされている Service Fabric のバージョンをクラスターで実行し続ける方法の詳細については、次のドキュメントを参照してください。

- [Azure クラスターの Service Fabric のバージョンをアップグレードする](service-fabric-cluster-upgrade.md)
- [スタンドアロン Windows Server クラスターの Service Fabric のバージョンをアップグレードする](service-fabric-cluster-upgrade-windows-server.md)

サポートされている Service Fabric のバージョンとそのサポートの終了日の一覧を次に示します。

| **クラスター内の Service Fabric ランタイム** | **クラスター バージョンから直接アップグレードできます。** |**互換性のある SDK / NuGet パッケージのバージョン** | **サポートの終了日** |
| --- | --- |--- | --- |
| 5.3.121 より前のすべてのクラスター バージョン | 5.1.158* |バージョン 2.3 以前 |2017 年 1 月 20 日 |
| 5.3.* | 5.1.158.* |バージョン 2.3 以前 |2017 年 2 月 24 日 |
| 5.4.* | 5.1.158.* |バージョン 2.4 以前 |2017 年 5 月 10 日       |
| 5.5.* | 5.4.164.* |バージョン 2.5 以前 |2017 年 8 月 10 日    |
| 5.6.* | 5.4.164.* |バージョン 2.6 以前 |2017 年 10 月 13 日   |
| 5.7.* | 5.4.164.* |バージョン 2.7 以前 |2017 年 12 月 15 日  |
| 6.0.* | 5.6.205.* |バージョン 2.8 以前 |2018 年 3 月 30 日     |
| 6.1.* | 5.7.221.* |バージョン 3.0 以前 |2018 年 7 月 15 日      |
| 6.2.* | 6.0.232.* |バージョン 3.1 以前 |2018 年 10 月 26 日   |
| 6.3.* | 6.1.480.* |バージョン 3.2 以前 |2019 年 3 月 31 日  |
| 6.4.* | 6.2.301.* |バージョン 3.3 以前 |最新バージョンのため、終了日はありません |