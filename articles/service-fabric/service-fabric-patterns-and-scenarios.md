---
title: "Azure Service Fabric のパターンとシナリオ | Microsoft Docs"
description: "Service Fabric でマイクロサービスを設計、開発し、運用するために、ベスト プラクティスと実証済みの再利用可能なパターンについて説明します。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: d5aa75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/08/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: df7c127f4ab140cb26c82e723964f1d99f03131a
ms.lasthandoff: 03/09/2017


---
# <a name="service-fabric-patterns-and-scenarios"></a>Service Fabric のパターンとシナリオ
Azure Service Fabric を使用して大規模なマイクロサービスを構築することを検討している場合は、このサービスとしてのプラットフォーム (PaaS) を設計および構築した専門家から学んでください。 適切なアーキテクチャから始め、アプリケーションのリソースを最適化する方法を確認します。 「[Service Fabric Patterns and Practices (Service Fabric のパターンと実践)](https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=mudwqISGD_6005167344)」コースでは、Service Fabric のシナリオとアプリケーション領域について、実際のお客様からよく寄せられる質問に答えます。
 
ベスト プラクティスと実証済みの再利用可能なパターンを使用して、Service Fabric でマイクロサービスを設計、開発し、運用する方法を紹介します。 Service Fabric の概要を説明した後、クラスターの最適化とセキュリティ、レガシ アプリケーションの移行、大規模な IoT、ゲーム エンジンのホストなどを取り上げたトピックを深く掘り下げます。 さまざまなワークロードの継続的配信について説明し、Linux Support とコンテナーについても詳しく説明します。 

## <a name="introduction"></a>はじめに
ベスト プラクティスを紹介し、サービスとしてのインフラストラクチャ (IaaS) 上のサービスとしてのプラットフォーム (PaaS) の選択について説明します。 実証済みのアプリケーション設計原則に従う方法について詳しく説明します。

<table><tr><th>ビデオ</th><th>PowerPoint デッキ</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=N2KwbbSGD_6405167344">
<img src="./media/service-fabric-patterns-and-scenarios/intro.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=mudwqISGD_6005167344">Service Fabric の概要</a></td></tr>
</table>

## <a name="cluster-planning-and-management"></a>クラスターの計画と管理
Azure Service Fabric のこの側面では、容量計画、クラスターの最適化、クラスターのセキュリティについて説明します。

<table><tr><th>ビデオ</th><th>PowerPoint デッキ</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=cyDYZcSGD_2805167344">
<img src="./media/service-fabric-patterns-and-scenarios/cluster.png" WIDTH="360" HEIGHT="244">
</a></td><td> <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=E5B3nJSGD_805167344">クラスターの計画と管理</a></td></tr>
</table>

## <a name="hyper-scale-web"></a>ハイパースケール Web
可用性と信頼性、ハイパースケール、状態管理など、ハイパースケール Web に関する概念を確認します。

<table><tr><th>ビデオ</th><th>PowerPoint デッキ</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=NgldAdSGD_405167344">
<img src="./media/service-fabric-patterns-and-scenarios/hyperscaleweb.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=CPMLBLSGD_7705167344">ハイパースケール Web</a></td></tr>
</table>

## <a name="iot"></a>IoT
Azure IoT パイプライン、マルチテナント、大規模な IoT など、Azure Service Fabric のコンテキストにおけるモノのインターネット (IoT) について説明します。

<table><tr><th>ビデオ</th><th>PowerPoint デッキ</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=naFUVeSGD_1505167344">
<img src="./media/service-fabric-patterns-and-scenarios/iot.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=kfqFWMSGD_6205167344">IoT</a></td></tr>
</table>

## <a name="gaming"></a>ゲーム
ターンベースのゲーム、対話型ゲーム、既存のゲーム エンジンのホストについて説明します。

<table><tr><th>ビデオ</th><th>PowerPoint デッキ</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=6wECzeSGD_3805167344">
<img src="./media/service-fabric-patterns-and-scenarios/gaming.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=kfqFWMSGD_6205167344">ゲーム</a></td></tr>
</table>

## <a name="continuous-delivery"></a>継続的配信
Visual Studio Team Services による継続的インテグレーション/継続的配信、ワークフローの構築/パッケージ化/公開、マルチ環境のセットアップ、サービス パッケージ/共有などの概念について説明します。

<table><tr><th>ビデオ</th><th>PowerPoint デッキ</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=78h5ofSGD_305167344">
<img src="./media/service-fabric-patterns-and-scenarios/cd.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=VlENvOSGD_105167344">継続的配信</a></td></tr>
</table>

## <a name="migration"></a>移行
レガシ アプリケーションの移行だけでなく、クラウド サービスからの移行についても説明します。

<table><tr><th>ビデオ</th><th>PowerPoint デッキ</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=hd1cMgSGD_5605167344">
<img src="./media/service-fabric-patterns-and-scenarios/migration.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=GQAq4QSGD_8305167344">移行</a></td></tr>
</table>

## <a name="containers-and-linux-support"></a>コンテナーと Linux Support
"なぜコンテナーを使用するのか" という質問に答えます。 Windows コンテナー、Linux Support、Linux コンテナーのオーケストレーションのプレビューについて説明します。 さらに、.NET Core アプリを Linux に移行する方法も紹介します。

<table><tr><th>ビデオ</th><th>PowerPoint デッキ</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=V1ERJhSGD_305167344">
<img src="./media/service-fabric-patterns-and-scenarios/containers.png" WIDTH="360" HEIGHT="244">
</a></td><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/service-fabric-patterns-and-practices-16925?l=mlYsZRSGD_2105167344">コンテナーと Linux Support</a></td></tr>
</table>

## <a name="next-steps"></a>次のステップ
これで、Service Fabric のパターンとシナリオについて学習できました。次に、[クラスターを作成して管理する方法](service-fabric-deploy-anywhere.md)、[Cloud Services アプリを Service Fabric に移行する方法](service-fabric-cloud-services-migration-worker-role-stateless-service.md)、[継続的配信を設定する方法](service-fabric-set-up-continuous-integration.md)、[コンテナーをデプロイする方法](service-fabric-containers-overview.md)の詳細を確認します。

