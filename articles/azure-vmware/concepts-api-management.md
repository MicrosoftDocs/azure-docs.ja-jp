---
title: 概念 - API Management
description: Azure VMware Solution (AVS) 仮想マシン (VM) 上で実行されている API を API Management が保護する方法について説明します
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 62112bf3c0bf551232e09e5910e3eaae228dc202
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85306751"
---
# <a name="api-management-to-publish-and-protect-apis-running-on-avs-based-vms"></a>AVS ベースの VM で実行されている API を発行および保護するための API Management

Microsoft Azure [API Management](https://azure.microsoft.com/services/api-management/) を使用すると、開発者や DevOps チームは、内部または外部のコンシューマーに安全に発行できます。

複数の SKU で提供されていますが、Developer および Premium SKU のみで、Azure Virtual Network 統合を使用して、Azure VMware Solution (AVS) ワークロードで実行されている API を発行することができます。 これら 2 つの SKU は、API Management サービスとバックエンド間の接続を安全に有効にします。 Developer SKU は開発とテストを目的としており、Premium SKU は運用環境のデプロイ向けです。

AVS 仮想マシン (VM) 上で実行されるバックエンド サービスの場合、既定では API Management の構成はオンプレミスのバックエンド サービスと同じです。 内部デプロイと外部デプロイの両方について、API Management は、AVS 側でバックエンド サーバーが NSX Load Balancer の後ろに配置されている場合、ロード バランサーの仮想 IP (VIP) をバックエンド エンドポイントとして構成します。

## <a name="external-deployment"></a>外部デプロイ

外部デプロイは、パブリック エンドポイントを使用して外部ユーザーによって使用される API を発行します。 開発者と DevOps エンジニアは、Azure portal または PowerShell と API Management 開発者ポータルを使用して API を管理できます。

外部配置ダイアグラムには、プロセス全体と関連するアクター (上部に表示) が表示されます。 アクターは次のとおりです。

- **Administrator(s):** 管理者または DevOps チームを表します。Azure portal および PowerShell や Azure DevOps などの と自動化メカニズムを通じて、AVS を管理します。

- **Users:** 公開されている API のコンシューマーを表し、API を使用するユーザーとサービスの両方を表します。

トラフィック フローは API Management インスタンスを通過します。これにより、バックエンド サービスが抽象化され、ハブ仮想ネットワークに接続されます。 ExpressRoute ゲートウェイがトラフィックを ExpressRoute Global Reach チャネルにルーティングし、その着信トラフィックをさまざまなバックエンド サービス インスタンスに分散する NSX Load Balancer に到達します。

API Management には Azure パブリック API があり、Azure DDOS Protection サービスをアクティブ化することをお勧めします。 

:::image type="content" source="media/api-management/external-deployment.png" alt-text="外部デプロイ - AVS 向けの API Management":::


## <a name="internal-deployment"></a>内部デプロイ

内部デプロイは、内部ユーザーまたはシステムによって使用される API を発行します。 DevOps チームおよび API 開発者は、外部デプロイと同じ管理ツールと開発者ポータルを使用します。

内部デプロイでは [Azure Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md) を使用してその機能を活用し、API 向けにパブリックのセキュリティ保護されたエンドポイントを作成し、さまざまなシナリオを可能にするハイブリッド デプロイを作成できます。  API はその機能を活用し、さまざまなシナリオを可能にするハイブリッドデプロイを作成します。

* 内部と外部のコンシューマーの両方における消費用に同一の API Management リソースを使用する。

* 単一の API Management リソースを持ち、定義した API の一部を外部コンシューマーが利用できるようにする。

* パブリック インターネットから API Management へのアクセスのオン/オフを切り替える簡単な方法を提供する。

次の配置ダイアグラムは、内部または外部のコンシューマーを示しています。それぞれが同じ API または異なる API にアクセスします。

内部デプロイでは、API は同じ API Management インスタンスに公開されます。 API Management の前に、 Azure Web Application Firewall (WAF) 機能がアクティブになっており、トラフィックをフィルター処理するための一連の HTTP リスナーとルールを備えた Application Gateway がデプロイされています。これは、AVS で実行されているバックエンド サービスのサブセットのみを公開します。

* 内部トラフィックは、トラフィック規則が確立されている場合は ExpressRoute ゲートウェイ経由で Azure Firewall の後に API Management にルーティングされるか、直接 API Management にルーティングされます。  

* 外部トラフィックは、API Management の外部保護レイヤーを使用する Application Gateway を通じて Azure に入ります。


:::image type="content" source="media/api-management/internal-deployment.png" alt-text="内部デプロイ - AVS 向けの API Management":::