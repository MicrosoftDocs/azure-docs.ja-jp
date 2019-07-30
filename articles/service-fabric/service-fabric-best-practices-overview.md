---
title: Azure Service Fabric のアプリケーションとクラスターに関するベスト プラクティス | Microsoft Docs
description: Service Fabric のクラスターとアプリケーションの管理に関するベスト プラクティスです。
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 5fdbd3f15b11e4c3975ca29627d5984382bcf049
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206803"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Azure Service Fabric のアプリケーションとクラスターに関するベスト プラクティス

この記事では、Azure Service Fabric のアプリケーションとクラスターの管理に関するベスト プラクティスへのリンクを提供します。 運用環境の信頼性を最適化するために、これらのプラクティスを実装することを強くお勧めします。 [Service Fabric クラスター テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates)のいずれかを使用して運用環境のソリューションの設計を開始するか、または既存のテンプレートを更新してこれらのプラクティスを組み込みます。

## <a name="security"></a>セキュリティ

* [セキュリティに関するベスト プラクティス](service-fabric-best-practices-security.md)

## <a name="networking"></a>ネットワーク

* [ネットワークに関するベスト プラクティス](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>コンピューティングの計画とスケーリング

* [コンピューティングのスケーリングに関するベスト プラクティス](service-fabric-best-practices-capacity-scaling.md)
* [コンピューティング能力の計画](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>コードとしてのインフラストラクチャ

* [コードとしてのインフラストラクチャの実装に関するベスト プラクティス](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>監視と診断

* [クラスターの監視と診断に関するベスト プラクティス](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>アプリケーションの設計

* [アプリケーション設計のベスト プラクティス](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>チェック リスト

前のセクションで推奨されたプラクティスを実装した後は、運用環境の準備状況チェックリストのすべてのベスト プラクティスを統合したことを確認します。
* [Azure Service Fabric の運用環境の準備状況チェックリスト](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>次の手順

* Windows Server を実行している VM またはコンピューター上にクラスターを作成する:[Windows Server 用の Service Fabric クラスターの作成](service-fabric-cluster-creation-for-windows-server.md)
* Linux を実行している VM またはコンピューター上にクラスターを作成する:[Linux クラスターの作成](service-fabric-cluster-creation-via-portal.md)
* Service Fabric のトラブルシューティング:[トラブルシューティング ガイド](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)