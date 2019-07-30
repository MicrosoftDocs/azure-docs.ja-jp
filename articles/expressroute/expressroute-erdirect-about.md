---
title: ExpressRoute Direct について - Azure | Microsoft Docs
description: このページでは、ExpressRoute Direct の概要を示します
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/06/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: d7add3d509427d72e23d61d1777db7941c7550a4
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657256"
---
# <a name="about-expressroute-direct"></a>ExpressRoute Direct について

ExpressRoute Direct を使用すると、世界中に戦略的に分散されたピアリングの場所で Microsoft のグローバル ネットワークに直接接続できます。 ExpressRoute Direct では、大規模なアクティブ/アクティブ接続をサポートするデュアル 100 Gbps または 10 Gbps 接続が提供されます。

以下に、ExpressRoute Direct で提供される主な機能の一部を示します。

* Storage や Cosmos DB などのサービスへの大規模なデータ インジェスト
* 規制対象となっていて、専用の分離された接続が必要な、銀行、政府機関、小売などの業種に向けた物理的な分離性
* 事業単位に基づいた回線配分の詳細な制御

## <a name="onboard-to-expressroute-direct"></a>ExpressRoute Direct へのオンボード

ExpressRoute Direct を利用する前に、まず、サブスクリプションを登録する必要があります。 登録するには、以下の詳細を含め、サブスクリプション ID を記載して、<ExpressRouteDirect@microsoft.com> にメールを送信します。

* **ExpressRoute Direct** で実行しようとしているシナリオ
* 場所設定。すべての場所の完全なリストについては、[パートナーとピアリングの場所](expressroute-locations-providers.md)に関するページを参照してください。
* 実装のタイムライン
* その他の質問について

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>サービス プロバイダーを使用する ExpressRoute と ExpressRoute Direct

| **サービス プロバイダーを使用する ExpressRoute** | **ExpressRoute Direct** | 
| --- | --- |
| サービス プロバイダーを利用して、既存のインフラストラクチャへの高速オンボードと接続を有効にします | 100 Gbps/10 Gbps インフラストラクチャとすべてのレイヤーの完全な管理が必要です
| イーサネットや MPLS を含む、何百ものプロバイダーと統合されます | 規制対象の業種と大量のデータ インジェストのための直接/専用容量 |
| 50 Mbps ～ 10 Gbps 回線 SKU | 顧客は 100 Gbps ExpressRoute Direct で以下の回線 SKU の組み合わせを選択できます。 <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> 顧客は 10 Gbps ExpressRoute Direct で以下の回線 SKU の組み合わせを選択できます。<ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>
| シングル テナント用に最適化 | シングル テナント/クラウド サービス プロバイダー/複数のビジネス ユニット用に最適化

## <a name="expressroute-direct-circuits"></a>ExpressRoute Direct の回線

Microsoft Azure ExpressRoute を利用すれば、接続プロバイダーが提供するプライベート接続で、オンプレミスのネットワークを Microsoft Cloud に拡張できます。 ExpressRoute では、Microsoft Azure、Office 365、Dynamics 365 などの Microsoft クラウド サービスへの接続を確立できます。  

各ピアリングの場所からは Microsoft のグローバル ネットワークにアクセスできます。また、既定で地理的ゾーン内のすべてのリージョンにアクセス可能であり、Premium 回線を使用してすべてのグローバル リージョンにアクセスすることができます。  

ほとんどのシナリオの機能は、ExpressRoute サービス プロバイダーを利用して操作する回線と同じです。 ExpressRoute Direct を使用して提供される新しい機能をサポートするため、また細分性を高めるために、ExpressRoute Direct 回線に存在する特定の主な機能があります。

## <a name="circuit-skus"></a>回線 SKU

ExpressRoute Direct では、Azure Storage やその他のビッグ データ サービスへの大量のデータ インジェスト シナリオがサポートされます。 また、100 ExpressRoute Direct の ExpressRoute 回線でも、**40 Gbps** および **100 Gbps** の回線 SKU がサポートされるようになりました。 物理ポート ペアは、**100 Gbps または 10 Gbps** のみであり、複数の仮想回線を備えることができます。 回線のサイズ:

| **100 Gbps ExpressRoute Direct** | **10 Gbps ExpressRoute Direct** | 
| --- | --- |
| **サブスクライブされた帯域幅**: 200 Gbps | **サブスクライブされた帯域幅**: 20 Gbps |
| <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> | <ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>

## <a name="vlan-tagging"></a>VLAN タグ付け

ExpressRoute Direct では、QinQ と Dot1Q の両方の VLAN タグ付けがサポートされます。

* **QinQ VLAN タグ付け**では、ExpressRoute 回線ごとにルーティング ドメインを分離することができます。 Azure では回線の作成時に S-Tag が動的に割り当てられ、変更することはできません。 回線 (プライベートおよび Microsoft) の各ピアリングでは、VLAN として一意の C-Tag が利用されます。 C-Tag を、ExpressRoute Direct ポートの回線全体で一意にする必要はありません。

* **Dot1Q VLAN タグ付け**では、ExpressRoute Direct ポート ペアごとに 1 つの VLAN タグ付けが可能です。 ピアリングで使用される C-Tag は、ExpressRoute Direct ポート ペアのすべての回線とピアリング全体で一意である必要があります。

## <a name="workflow"></a>ワークフロー

[![ワークフロー](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

ExpressRoute Direct では、Microsoft グローバル ネットワークへのアクティブ/アクティブ冗長接続でも同じエンタープライズ レベルの SLA が提供されます。 ExpressRoute インフラストラクチャは冗長です。Microsoft グローバル ネットワークへの接続は冗長かつ多様であり、顧客要件に応じてスケーリングされます。 

## <a name="next-steps"></a>次の手順

[ExpressRoute Direct を構成する](expressroute-howto-erdirect.md)
