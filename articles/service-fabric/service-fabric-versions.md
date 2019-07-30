---
title: Azure Service Fabric でサポートされているクラスターのバージョン | Microsoft Docs
description: Azure Service Fabric でのクラスターのバージョンについて説明します。
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
ms.date: 07/03/2019
ms.author: aljo
ms.openlocfilehash: 030e57512d10f31ca03dff2f99e57c305de49e97
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565993"
---
# <a name="supported-service-fabric-versions"></a>サポートされている Service Fabric のバージョン

クラスターでは常に、サポートされている Azure Service Fabric のバージョンが実行されている状態にします。 Service Fabric の新しいバージョンのリリースが発表された日から最短で 60 日後には、以前のバージョンのサポートが終了します。 新しいリリースについては、[Service Fabric チーム ブログ](https://blogs.msdn.microsoft.com/azureservicefabric/)上でお知らせします。

サポートされている Service Fabric のバージョンをクラスターにおいて継続して実行する方法の詳細については、次のドキュメントを参照してください。

- [Azure Service Fabric クラスターのアップグレード](service-fabric-cluster-upgrade.md)
- [スタンドアロン Windows Server クラスター上で実行される Service Fabric のバージョンをアップグレードする](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>サポートされているバージョン

次の表に、Service Fabric のバージョンとサポート終了日の一覧を示します。

| クラスター内の Service Fabric ランタイム | クラスター バージョンから直接アップグレードできる |互換性のある SDK または NuGet パッケージのバージョン | サポート終了 |
| --- | --- |--- | --- |
| 5\.3.121 より前のすべてのクラスター バージョン | 5.1.158.* |バージョン 2.3 以前 |2017 年 1 月 20 日 |
| 5.3.* | 5.1.158.* |バージョン 2.3 以前 |2017 年 2 月 24 日 |
| 5.4.* | 5.1.158.* |バージョン 2.4 以前 |2017 年 5 月 10 日       |
| 5.5.* | 5.4.164.* |バージョン 2.5 以前 |2017 年 8 月 10 日    |
| 5.6.* | 5.4.164.* |バージョン 2.6 以前 |2017 年 10 月 13 日   |
| 5.7.* | 5.4.164.* |バージョン 2.7 以前 |2017 年 12 月 15 日  |
| 6.0.* | 5.6.205.* |バージョン 2.8 以前 |2018 年 3 月 30 日     |
| 6.1.* | 5.7.221.* |バージョン 3.0 以前 |2018 年 7 月 15 日      |
| 6.2.* | 6.0.232.* |バージョン 3.1 以前 |2018 年 10 月 26 日   |
| 6.3.* | 6.1.480.* |バージョン 3.2 以前 |2019 年 3 月 31 日  |
| 6.4.* | 6.2.301.* |バージョン 3.3 以前 |2019 年 9 月 15 日 |
| 6.5.* | 6.4.617.* |バージョン 3.4 以前 |最新バージョンのため、終了日なし |

## <a name="supported-operating-systems"></a>サポートされているオペレーティング システム

次の表に、サポートされている Service Fabric のバージョンに対応しているサポート対象のオペレーティング システムを示します。

| オペレーティング システム | サポートされる最も古い Service Fabric のバージョン |
| --- | --- |
| Windows Server 2012 R2 | すべてのバージョン |
| Windows Server 2016 | すべてのバージョン |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4. |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

## <a name="supported-version-names"></a>サポートされているバージョン

次の表に、Service Fabric のバージョン名と対応するバージョン番号を示します。

| バージョン名 | Windows のバージョン番号 | Linux のバージョン番号 |
| --- | --- | --- |
| 5.3 RTO | 5.3.121.9494 | NA |
| 5.3 CU1 | 5.3.204.9494 | NA |
| 5.3 CU2 | 5.3.301.9590 | NA |
| 5.3 CU3 | 5.3.311.9590 | NA |
| 5.4 CU2 | 5.4.164.9494 | NA |
| 5.5 CU1 | 5.5.216.0    | NA |
| 5.5 CU2 | 5.5.219.0    | NA |
| 5.5 CU3 | 5.5.227.0    | NA |
| 5.5 CU4 | 5.5.232.0    | NA |
| 5.6 RTO | 5.6.204.9494 | NA |
| 5.6 CU2 | 5.6.210.9494 | NA |
| 5.6 CU3 | 5.6.220.9494 | NA |
| 5.7 RTO | 5.7.198.9494 | NA |
| 5.7 CU4 | 5.7.221.9494 | NA |
| 6.0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6.0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6.0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6.1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6.1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6.1 CU3 | 6.1.472.9494 | NA |
| 6.1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6.2 RTO | 6.2.269.9494 | 6.2.184.1 | 
| 6.2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6.2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6.2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6.3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6.3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6.3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6.4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6.4 CU2 | 6.4.622.9590 | NA |
| 6.4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6.4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6.4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6.4 CU6 | 6.4.658.9590 | NA |
| 6.4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6.4 CU8 | 6.4.670.9590 | NA |
| 6.5 RTO | 6.5.639.9590 | 6.5.435.1 |
| 6.5 CU1 | 6.5.641.9590 | 6.5.454.1 |