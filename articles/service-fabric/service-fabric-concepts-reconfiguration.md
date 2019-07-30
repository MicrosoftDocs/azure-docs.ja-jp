---
title: Azure Service Fabric の再構成 | Microsoft Docs
description: Service Fabric のパーティションの再構成について説明します。
services: service-fabric
documentationcenter: .net
author: appi101
manager: anuragg
editor: ''
ms.assetid: d5ab75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: a24aa6aa1695a3d1166816b7960bdd7b551e1a37
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60882199"
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Azure Service Fabric の再構成
"*構成*" は、ステートフル サービスのパーティションのレプリカとそれらのロールと定義されます。

"*再構成*" は、1 つの構成を別の構成に移行するプロセスであり、 ステートフル サービスのパーティションのレプリカ セットに対する変更が行われます。 古い構成を "*以前の構成 (PC: previous configuration)* "、新しい構成を "*現在の構成 (CC: current configuration)* " と呼びます。 Azure Service Fabric の再構成プロトコルは、レプリカ セットを変更している間、一貫性を保持し、可用性を維持します。

Failover Manager は、システムのさまざまなイベントに応答して再構成を開始します。 たとえば、プライマリに障害が発生した場合は、アクティブなセカンダリをプライマリに昇格させるための再構成が開始されます。 別の例として、アプリケーションのアップグレードに対する応答があります。ノードをアップグレードするために、プライマリを別のノードに移動しなければならない場合があります。

## <a name="reconfiguration-types"></a>再構成の種類
再構成は、2 種類に分類できます。

- プライマリが変更される再構成
    - **フェールオーバー**:フェールオーバーは、実行中のプライマリの障害に対する応答としての再構成です。
    - **SwapPrimary**:スワップは、Service Fabric が、実行中のプライマリをあるノードから別のノードに移動する必要がある場合の再構成です。通常、これは、負荷分散やアップグレードに応答するために行われます。

- プライマリが変更されない再構成

## <a name="reconfiguration-phases"></a>再構成のフェーズ
再構成は、さまざまなフェーズで進行します。

- **フェーズ 0**:このフェーズはプライマリ スワップ再構成で発生し、現在のプライマリの状態が新しいプライマリに転送され、現在のプライマリがアクティブな セカンダリに遷移します。

- **フェーズ 1**:このフェーズは再構成中に発生し、プライマリが変更されます。 このフェーズ中に、Service Fabric は現在のレプリカから正しいプライマリを識別します。 プライマリ スワップ再構成では、新しいプライマリが既に選択されているため、このフェーズは必要ありません。 

- **フェーズ 2**:このフェーズ中に、Service Fabric は、現在の構成のほとんどのレプリカで、すべてのデータが利用可能であることを確認します。

上記以外の内部使用のみのさまざまなフェーズがあります。

## <a name="stuck-reconfigurations"></a>再構成の停止
再構成は、さまざまな理由で "*停止*" することがあります。 一般的な理由として、以下が挙げられます。

- **レプリカのダウン**:一部の再構成フェーズでは、構成に含まれるレプリカの大半が起動している必要があります。
- **ネットワークまたは通信の問題**:再構成では、異なるノード間のネットワーク接続が必要です。
- **API のエラー**:再構成プロトコルでは、サービスの実装で特定の API が完了している必要があります。 たとえば、信頼性の高いサービスでキャンセル トークンを考慮しなかった場合、プライマリ スワップ再構成は停止します。

System.FM、System.RA、System.RAP などのシステム コンポーネントからの正常性レポートを使用して、どこで再構成が停止しているかを診断できます。 これらの正常性レポートについては、[システム正常性レポート](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順
Service Fabric の概念について詳しくは、次の記事をご覧ください。

- [Reliable Services のライフサイクル - C#](service-fabric-reliable-services-lifecycle.md)
- [システム正常性レポート](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [レプリカとインスタンス](service-fabric-concepts-replica-lifecycle.md)
