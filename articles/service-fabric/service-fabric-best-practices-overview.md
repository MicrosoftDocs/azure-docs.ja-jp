---
title: Azure Service Fabric のアプリケーションとクラスターに関するベスト プラクティス
description: Azure Service Fabric を使用してクラスター、アプリ、およびサービスを管理するためのベスト プラクティスと設計上の考慮事項。
author: peterpogorski
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 86a02fd489ca0eec61b798db7136f963277f6c82
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261081"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Azure Service Fabric のアプリケーションとクラスターに関するベスト プラクティス

この記事では、Azure Service Fabric のアプリケーションとクラスターの管理に関するベスト プラクティスへのリンクを提供します。 運用環境の信頼性を最適化するために、これらのプラクティスを実装することを強くお勧めします。 [Service Fabric クラスター テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates)のいずれかを使用して運用環境のソリューションの設計を開始するか、または既存のテンプレートを更新してこれらのプラクティスを組み込みます。

## <a name="security"></a>Security

* [セキュリティに関するベスト プラクティス](service-fabric-best-practices-security.md)

## <a name="networking"></a>ネットワーク

* [ネットワークに関するベスト プラクティス](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>コンピューティングの計画とスケーリング

* [コンピューティングのスケーリングに関するベスト プラクティス](service-fabric-best-practices-capacity-scaling.md)
* [コンピューティング能力の計画](./service-fabric-cluster-capacity.md)

## <a name="infrastructure-as-code"></a>コードとしてのインフラストラクチャ

* [コードとしてのインフラストラクチャの実装に関するベスト プラクティス](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>監視と診断

* [クラスターの監視と診断に関するベスト プラクティス](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>アプリケーションの設計

* [アプリケーション設計のベスト プラクティス](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>チェック リスト

前のセクションで推奨されたプラクティスを実装した後は、運用環境の準備状況チェックリストのすべてのベスト プラクティスを統合したことを確認します。
* [Azure Service Fabric の運用環境の準備状況チェックリスト](./service-fabric-production-readiness-checklist.md)

## <a name="next-steps"></a>次のステップ

* Windows Server を実行している VM またはコンピューター上にクラスターを作成する:[Windows Server 用の Service Fabric クラスターの作成](service-fabric-cluster-creation-for-windows-server.md)
* Linux を実行している VM またはコンピューター上にクラスターを作成する:[Linux クラスターの作成](service-fabric-cluster-creation-via-portal.md)
* Service Fabric のトラブルシューティング:[トラブルシューティング ガイド](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
