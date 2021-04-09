---
title: Azure Firewall Manager のアーキテクチャのオプション
description: Azure Firewall Manager で使用するハブ仮想ネットワーク アーキテクチャとセキュリティ保護付き仮想ハブ アーキテクチャを比較対照します。
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: article
ms.date: 09/14/2020
ms.author: victorh
ms.openlocfilehash: 71ff23e749139087f24da406474403167dcc1c0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90563150"
---
# <a name="what-are-the-azure-firewall-manager-architecture-options"></a>Azure Firewall Manager のアーキテクチャのオプション

Azure Firewall Manager は、次の 2 種類のネットワーク アーキテクチャに対するセキュリティ管理機能を備えています。

- **セキュリティ保護付き仮想ハブ**

   [Azure Virtual WAN ハブ](../virtual-wan/virtual-wan-about.md#resources)は、ハブとスポークのアーキテクチャを簡単に作成できる Microsoft の管理対象リソースです。 セキュリティ ポリシーとルーティング ポリシーがそのようなハブに関連付けられている場合は、" *[セキュリティ保護付き仮想ハブ](secured-virtual-hub.md)* " と呼ばれます。 
- **ハブ仮想ネットワーク**

   自分で作成して管理できる標準の Azure 仮想ネットワークです。 そのようなハブにセキュリティ ポリシーが関連付けられている場合は、"*ハブ仮想ネットワーク*" と呼ばれます。 現時点では、Azure Firewall ポリシーのみがサポートされています。 ワークロード サーバーが含まれるスポーク仮想ネットワークとサービスをピアリングすることができます。 どのスポークにもピアリングされていないスタンドアロンの仮想ネットワークでファイアウォールを管理することもできます。

## <a name="comparison"></a>比較

次の表では、これら 2 つのアーキテクチャ オプションを比較します。組織のセキュリティ要件に適しているものを判断するのに役立ちます。


|  |**ハブ仮想ネットワーク**|**セキュリティ保護付き仮想ハブ**  |
|---------|---------|---------|
|**基になるリソース**     |仮想ネットワーク|Virtual WAN ハブ|
|**ハブとスポーク**     |仮想ネットワーク ピアリングを使用|ハブ仮想ネットワーク接続を使用して自動化|
|**オンプレミス接続**     |最大 10 Gbps とサイト間接続 30 個の VPN Gateway、ExpressRoute|よりスケーラブルな最大 20 Gbps とサイト間接続 1000 個の VPN Gateway、ExpressRoute|
|**SDWAN を使用したブランチ接続の自動化**      |サポートされていません|サポートされています|
|**リージョンごとのハブ**     |リージョンごとに複数の仮想ネットワーク|リージョンごとに 1 つの仮想ハブ。 複数の Virtual WAN で複数のハブが可能|
|**Azure Firewall – 複数のパブリック IP アドレス**      |お客様側で準備|自動生成|
|**Azure Firewall Availability Zones**     |サポートされています|まだ使用できません|
|**サードパーティのサービスとしてのセキュリティ パートナーによる高度なインターネット セキュリティ**     |お客様が自分で選択したパートナー サービスへの VPN 接続を確立して管理|セキュリティ パートナー プロバイダーのフローとパートナー管理のエクスペリエンスによって自動化|
|**ハブにトラフィックをルーティングするための集中ルート管理**     |お客様が管理するユーザー定義ルート|BGP を使用してサポート|
|**複数のセキュリティ プロバイダーのサポート**|サードパーティのファイアウォールへの強制トンネリングを手動で構成してサポート|2 つのセキュリティ プロバイダーの自動サポート:プライベート トラフィックのフィルタリング用の Azure Firewall とインターネット フィルタリング用のサードパーティ|
|**Application Gateway上の Web アプリケーション ファイアウォール** |Virtual Network でサポート|現在はスポーク ネットワークでサポート|
|**ネットワーク仮想アプライアンス**|Virtual Network でサポート|現在はスポーク ネットワークでサポート|
|**Azure DDoS Protection Standard のサポート**|はい|いいえ|

## <a name="next-steps"></a>次のステップ

- [Azure Firewall Manager のデプロイ概要](deployment-overview.md)を確認する
- [セキュリティで保護された仮想ハブ](secured-virtual-hub.md)について学習します。