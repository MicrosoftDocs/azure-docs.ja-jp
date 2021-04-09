---
title: Azure Defender for DNS - 利点と機能
description: Azure Defender for DNS の利点と機能について説明します。
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: dffb505719e6778adfdd8e99f62790df9ebd615a
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100697"
---
# <a name="introduction-to-azure-defender-for-dns"></a>Azure Defender for DNS の概要

[Azure DNS](../dns/dns-overview.md) は、DNS ドメインのホスティング サービスであり、Microsoft Azure インフラストラクチャを使用した名前解決を提供します。 Azure でドメインをホストすることで、その他の Azure サービスと同じ資格情報、API、ツール、課金情報を使用して DNS レコードを管理できます。

Azure Defender for DNS は、次の方法によってクラウド リソースをいっそう強固に保護します。

- Azure リソースからのすべての DNS クエリを継続的に監視する
- 高度なセキュリティ分析を実行して不審なアクティビティについてアラートする

## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|プレビュー<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|価格:|**Azure Defender for DNS** の課金については、「[Security Center の価格](https://azure.microsoft.com/pricing/details/security-center/)」をご覧ください|
|クラウド:|![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![No](./media/icons/no-icon.png) ナショナル/ソブリン (US Gov、China Gov、その他の Gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-dns"></a>Azure Defender for DNS の利点

Azure Defender for DNS は、次のような問題からの保護対策となります。

- Azure リソースからのデータ流出 (DNS トンネリングを使用)
- C&C サーバーを使用したマルウェアの通信
- 悪意のあるドメインとの通信 (フィッシング、クリプト マイニングなど)
- DNS 攻撃 - 悪意のある DNS リゾルバとの通信 

Azure Defender for DNS によって提供されるアラートの全一覧は、[アラートのリファレンス ページ](alerts-reference.md#alerts-dns)に掲載されています。

## <a name="dependencies"></a>依存関係

Azure Defender for DNS では、エージェントは使用されません。 

DNS レイヤーを保護するためには、各サブスクリプションについて、「[Azure Defender を有効にする](enable-azure-defender.md)」の説明に従って Azure Defender for DNS を有効にします。


## <a name="next-steps"></a>次のステップ

この記事では、Azure Defender for DNS について説明しました。 関連資料については、次の記事をご覧ください。 

- セキュリティ アラートは、Security Center によって生成される場合もあれば、Security Center がさまざまなセキュリティ製品から受信する場合もあります。 それらすべてのアラートを Azure Sentinel、サードパーティの SIEM、またはその他の外部ツールにエクスポートする場合は、[SIEM へのアラートのエクスポート](continuous-export.md)に関するページの手順に従ってください。

- > [!div class="nextstepaction"]
    > [Azure Defender を有効にする](enable-azure-defender.md)