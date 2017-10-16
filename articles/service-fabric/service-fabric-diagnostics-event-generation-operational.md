---
title: "Azure Service Fabric 稼動チャネル | Microsoft Docs"
description: "Azure Service Fabric クラスターの稼動チャネル内に生成されるログの包括的な一覧。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/19/2017
ms.author: dekapur
ms.openlocfilehash: 934719868ab9968db352db2b440014d35dbc0274
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="operational-channel"></a>稼動チャネル 

稼動チャネルは、Service Fabric によりノードやクラスター上で実行される高水準のアクションからのログで構成されます。 クラスターで「診断」を有効にすると、クラスター上に Azure 診断エージェントがデプロイされ、既定で稼動チャネルからのログを読み取るように構成されます。 より多くのログやパフォーマンス カウンターを収集するようにクラスターの診断構成を変更するには、[Azure 診断エージェント](service-fabric-diagnostics-event-aggregation-wad.md)の構成に関する詳細をご確認ください。 

## <a name="operational-channel-logs"></a>稼動チャネルのログ 

Service Fabric により稼動チャネル内に生成されるログの包括的な一覧を次に示します。 

| EventId | 名前 | ソース (タスク) | Level |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | 情報 |
| 25621 | NodeOpenedSuccess | FabricNode | 情報 |
| 25622 | NodeOpenedFailed | FabricNode | 情報 |
| 25623 | NodeClosing | FabricNode | 情報 |
| 25624 | NodeClosed | FabricNode | 情報 |
| 25625 | NodeAborting | FabricNode | 情報 |
| 25626 | NodeAborted | FabricNode | 情報 |
| 29627 | ClusterUpgradeStart | CM | 情報 |
| 29628 | ClusterUpgradeComplete | CM | 情報 |
| 29629 | ClusterUpgradeRollback | CM | 情報 |
| 29630 | ClusterUpgradeRollbackComplete | CM | 情報 |
| 29631 | ClusterUpgradeDomainComplete | CM | 情報 |
| 23074 | ContainerActivated | ホスティング | 情報 |
| 23075 | ContainerDeactivated | ホスティング | 情報 |
| 29620 | ApplicationCreated | CM | 情報 |
| 29621 | ApplicationUpgradeStart | CM | 情報 |
| 29622 | ApplicationUpgradeComplete | CM | 情報 |
| 29623 | ApplicationUpgradeRollback | CM | 情報 |
| 29624 | ApplicationUpgradeRollbackComplete | CM | 情報 |
| 29625 | ApplicationDeleted | CM | 情報 |
| 29626 | ApplicationUpgradeDomainComplete | CM | 情報 |
| 18566 | ServiceCreated | FM | 情報 |
| 18567 | ServiceDeleted | FM | 情報 |

## <a name="next-steps"></a>次のステップ

* Service Fabric における[プラットフォーム レベルでのイベントの生成](service-fabric-diagnostics-event-generation-infra.md)一般に関する詳細情報
* より多くのログを収集するための [Azure 診断](service-fabric-diagnostics-event-aggregation-wad.md)の構成の変更
* 稼動チャネルのログを参照するための [Application Insights の設定](service-fabric-diagnostics-event-analysis-appinsights.md)
