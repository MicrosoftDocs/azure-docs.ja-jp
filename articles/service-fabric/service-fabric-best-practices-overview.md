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
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: a0174a5442de7a10e45e9dc2a2f43f5f401d1a69
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805072"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Azure Service Fabric のアプリケーションとクラスターに関するベスト プラクティス

Azure Service Fabric のアプリケーションとクラスターを問題なく管理するには、運用環境の信頼性を最適化するために実行することを強くお勧めする操作があります。このドキュメントで定義されている操作を実行し、[Azure サンプルの Service Fabric クラスター テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates)のいずれかを選択して運用ソリューションの設計を始めるか、既存のテンプレートを変更してこれらのプラクティスを組み込んでください。

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

## <a name="checklist"></a>チェック リスト

上記のすべてのセクションを完了した後、運用環境の準備状況チェックリストのすべてのベスト プラクティスを統合したことを確認します。
* [Azure Service Fabric の運用環境の準備状況チェックリスト](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>次の手順

* Windows Server を実行している VM またはコンピューター上にクラスターを作成する:[Windows Server 用の Service Fabric クラスターの作成](service-fabric-cluster-creation-for-windows-server.md)
* Linux を実行している VM またはコンピューター上にクラスターを作成する:[Linux クラスターの作成](service-fabric-cluster-creation-via-portal.md)
* トラブルシューティング:[Service Fabric のトラブルシューティング ガイド](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)