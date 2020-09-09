---
title: Azure Security Center の価格レベル
description: Azure Security Center は Free と Standard という 2 つのレベルで提供されています。 このページでは、Free から Standard にアップグレードする方法を示します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2020
ms.author: memildin
ms.openlocfilehash: 882f8ab7e4f55809759f3a94fd04fb2c410fe188
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089721"
---
# <a name="upgrade-to-standard-tier-for-enhanced-security"></a>Standard レベルにアップグレードしてセキュリティを高める

Azure Security Center は、Azure、オンプレミス、他のクラウドで実行されているワークロードの統合セキュリティ管理と高度な脅威保護を実現します。 ハイブリッド クラウド ワークロードの可視化と制御、脅威にさらされる機会を減らす積極的防御、急速に進化するサイバー攻撃への対応に役立つインテリジェント検出などの機能が提供されます。

## <a name="pricing-tiers"></a>価格レベル
Azure Security Center は 2 つのレベルで提供されます。

- Azure portal 内の Azure Security Center ダッシュボードに初めてアクセスしたとき、または API を介してプログラムで有効にした場合は、すべての Azure サブスクリプションで **Free** レベルが有効になります。 Free レベルは、セキュリティ ポリシー、継続的なセキュリティ評価、および Azure リソースを保護するための実践的なセキュリティに関する推奨事項を提供します。

- **Standard** レベルは、Free レベルの機能をプライベートおよびその他のパブリック クラウドで実行されているワークロードまで拡張したもので、統合されたセキュリティの管理と脅威の保護をハイブリッド クラウド ワークロード全体で提供します。 Standard レベルでは、脅威の防止機能も追加されます。これは、組み込みの行動分析と機械学習を利用して、各種攻撃やゼロデイ攻撃、アクセスやアプリケーション制御を特定し、ネットワーク攻撃やマルウェアなどによる侵害を減らします。 さらに、Standard レベルでは、仮想マシンの脆弱性スキャンが追加されます。 Standard レベルは無料でお試しいただけます。 Security Center Standard では、VM、仮想マシン スケール セット、App Service、SQL サーバー、ストレージ アカウントなどの Azure リソースがサポートされます。 Azure Security Center Standard をお使いの場合、リソースの種類に基づいてサポートをオプトアウトすることができます。 

VM に対する Free レベルのセキュリティ評価のほとんどでは、Standard レベルのセキュリティ アラートの多くと同様、Log Analytics エージェントのインストールが必要となります。 Security Center の自動プロビジョニングを有効にすることで、Azure VM のエージェントを自動的にデプロイすることができます。

## <a name="try-standard-tier-free-for-30-days"></a>Standard レベルを 30 日間無料で試用する
Standard レベルは、最初の 30 日間は無料です。 30 日経過した時点で、サービスの利用を継続することを選択した場合は、使用量に応じて自動的に課金が開始されます。

Azure サブスクリプション全体を Standard レベルにアップグレードできます。これは、サブスクリプション内のすべてのリソースによって継承されます。

Standard レベルを取得するには

1. **Security Center** メイン メニューの **[Pricing & settings]\(価格と設定\)** を選択します。
2. Standard にアップグレードするサブスクリプションを選択します。
3. **[価格レベル]** を選択します。
4. **[Standard]** を選択してアップグレードします。
5. **[保存]** をクリックします。

[![Security Center の価格](media/security-center-pricing/pricing-tier-page.png)](media/security-center-pricing/pricing-tier-page.png#lightbox)

> [!NOTE]
> 脅威保護機能を含む Security Center のすべての機能を有効にするには、Standard 価格レベルを、適用可能なワークロードを含むサブスクリプションに適用する必要があります。 ワークスペースの価格を構成しても、Just-In-Time VM アクセス、適応型アプリケーション制御、および Azure リソースのネットワーク検出は有効になりません。 
>
> **Azure Storage アカウント**の脅威保護は、サブスクリプション レベルまたはリソース レベルで有効にできます。
> **Azure SQL Database SQL サーバー**の脅威保護は、サブスクリプション レベルまたはリソース レベルで有効にできます。
> **Azure Database for MariaDB/ MySQL/ PostgreSQL** の脅威保護は、リソース レベルでのみ有効にできます。


## <a name="why-upgrade-to-standard"></a>Standard にアップグレードする理由
Security Center は、次のようなハイブリッド クラウド ワークロード用の強化されたセキュリティと脅威保護を提供します。

- **ハイブリッド セキュリティ** - オンプレミスとクラウドのすべてのワークロードのセキュリティを、統合された 1 つのビューで確認できます。 セキュリティ ポリシーを適用し、ハイブリッド クラウドのワークロードのセキュリティを継続的に評価することで、セキュリティ標準に確実に準拠できます。 ファイアウォールやその他のパートナー ソリューションなどのさまざまなソースから、セキュリティ データを収集、検索、分析します。
- **セキュリティ アラート** - 高度な分析と Microsoft Intelligent Security Graph を使用して、巧妙化していくサイバー攻撃に対応します。 組み込みの行動分析と機械学習を活用して、各種攻撃やゼロデイ攻撃を特定します。 ネットワーク、マシン、クラウド サービスに対する攻撃や侵害後のアクティビティを監視します。 対話型のツールと状況に応じた脅威インテリジェンスにより、調査を効率化します。
- **仮想マシンの脆弱性スキャン** - 脆弱性管理のための業界最高レベルのソリューションを提供するスキャナーを、すべての仮想マシンに簡単にデプロイできます。 結果の表示、調査、修復を Security Center 内で直接実行できます。 
- **アクセスとアプリケーションの制御** - 特定のワークロードに適応し、機械学習を活用したホワイトリスト登録の推奨事項を適用することで、マルウェアや他の望ましくないアプリケーションをブロックします。 Azure VM の管理ポートに対するジャスト イン タイムの制御されたアクセスで、ネットワーク攻撃対象領域を減らします。 ブルート フォースなどのネットワーク攻撃に対する露出が、これによって劇的に減少します。
- **コンテナーのセキュリティ機能** - コンテナー化された環境で、脆弱性管理とリアルタイムの脅威の防止が利用できます。 コンテナー レジストリ リソースを有効にする際、すべての機能が有効になるまでには最大 12 時間かかることがあります。 料金は、接続されたレジストリにプッシュされた一意のコンテナー イメージの数に基づいています。 イメージのスキャンが 1 回完了すると、変更されて再度プッシュされない限り、再度請求されることはありません。 




## <a name="next-steps"></a>次のステップ
この記事では、Security Center の価格について紹介しました。 Standard レベルの強化されたセキュリティと高度な脅威保護に関する詳細については、次の記事を参照してください。

- [Azure Security Center での脅威の防止](threat-protection.md)
- [Just-In-Time VM アクセスの制御](security-center-just-in-time.md)
- [コンテナーのセキュリティの概要](container-security.md)
- [ご利用の通貨とリージョンにおける価格の詳細](https://azure.microsoft.com/pricing/details/security-center/)