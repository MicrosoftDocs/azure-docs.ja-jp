---
title: "Azure Security Center の価格 | Microsoft Docs"
description: "この記事では、Azure Security Center の価格について説明します。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: cd4001a3280c83875f30b04d5d38caec26f4f0b1
ms.contentlocale: ja-jp
ms.lasthandoff: 09/13/2017

---
# <a name="azure-security-center-pricing"></a>Azure Security Center の価格
Azure Security Center は、Azure、オンプレミス、他のクラウドで実行されているワークロードの統合セキュリティ管理と高度な脅威保護を実現します。 ハイブリッド クラウド ワークロードの可視化と制御、脅威にさらされる機会を減らす積極的防御、急速に進化するサイバー攻撃への対応に役立つインテリジェント検出などの機能が提供されます。

## <a name="pricing-tiers"></a>価格レベル
Azure Security Center は 2 つのレベルで提供されます。

- **Free** レベルは、すべての Azure サブスクリプションで自動的に有効にされ、セキュリティ ポリシー、継続的なセキュリティ評価、および Azure リソースを保護するための実践的なセキュリティに関する推奨事項を提供します
- **Standard** レベルは、Free レベルの機能をプライベートおよびその他のパブリック クラウドで実行されているワークロードまで拡張したもので、統合されたセキュリティの管理と脅威の保護をハイブリッド クラウド ワークロード全体で提供します。 Standard レベルでは、高度な脅威検出機能も追加されます。これは、組み込みの行動分析と機械学習を利用して、各種攻撃やゼロデイ攻撃、アクセスやアプリケーション制御を特定し、ネットワーク攻撃やマルウェアなどによる侵害を減らします。 Standard レベルは、最初の 60 日間は無料です。

詳細については、「[Security Center の価格](https://azure.microsoft.com/pricing/details/security-center/)」を参照してください。

## <a name="try-standard-free-for-60-days"></a>Standard レベルのサービスを 60 日間無料で試用する
Standard レベルは、最初の 60 日間は無料です。 60 日経過した時点で、サービスの利用を継続することを選択した場合は、使用量に応じて自動的に課金が開始されます。

Azure サブスクリプション全体を Standard レベルにアップグレードできます。この場合、サブスクリプション内のすべてのリソースが Standard レベルを継承します。また、特定のリソース グループだけをアップグレードする固有のポリシーを定義することもできます。

Standard レベルを取得するには

1. **Security Center** のメイン メニューで、**[セキュリティ ポリシー]** を選択します。
2. Standard レベルにアップグレードするサブスクリプションを選択します。
3. **[Security policy]** ブレードで、**[価格レベル]** を選択します。
4. **[Standard]** を選択してアップグレードします。
5. [ **Save**] をクリックします。

![セキュリティ インシデント][1]

> [!NOTE]
> Security Center のすべての機能を有効にするには、Standard 価格レベルを適用可能な仮想マシンを含むサブスクリプションまたはリソース グループに適用する必要があります。 ワークスペースの価格設定の構成では、Just in time VM アクセス、アダプティブ アプリケーション コントロール、および Azure リソースのネットワーク検出は有効になりません。
>
>

## <a name="why-upgrade-to-standard"></a>Standard レベルにアップグレードする理由
Security Center は、次のようなハイブリッド クラウド ワークロード用の強化されたセキュリティと脅威保護を提供します。

- **ハイブリッド セキュリティ** - オンプレミスとクラウドのすべてのワークロードのセキュリティを、統合された 1 つのビューで確認できます。 セキュリティ ポリシーを適用し、ハイブリッド クラウドのワークロードのセキュリティを継続的に評価することで、セキュリティ標準に確実に準拠できます。 ファイアウォールやその他のパートナー ソリューションなどのさまざまなソースから、セキュリティ データを収集、検索、分析します。
- **高度な脅威検出** - 高度な分析と Microsoft インテリジェント セキュリティ グラフを使用して、巧妙化していくサイバー攻撃に対応します。  組み込みの行動分析と機械学習を利用して、各種攻撃やゼロデイ攻撃を特定します。 ネットワーク、マシン、クラウド サービスに対する攻撃や侵害後のアクティビティを監視します。 対話型のツールと状況に応じた脅威インテリジェンスにより、調査を効率化します。
- **アクセスとアプリケーションの制御** - 特定のワークロードに適応し、機械学習を活用したホワイトリスト登録の推奨事項を適用することで、マルウェアや他の望ましくないアプリケーションをブロックします。 Azure VM の管理ポートへの制御された Just-In-Time アクセスによってネットワーク攻撃対象領域を縮小することで、ブルート フォース攻撃などのネットワーク攻撃にさらされる機会を大幅に減らします。


## <a name="next-steps"></a>次のステップ
この記事では、Security Center の価格について紹介しました。 Standard レベルの強化されたセキュリティと高度な脅威保護に関する詳細については、次の記事を参照してください。

- [高度な脅威検出](security-center-threat-report.md)
- [[Just In Time VM アクセスの制御]](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-pricing/get-standard.png

