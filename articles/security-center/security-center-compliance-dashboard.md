---
title: チュートリアル:規制に対するコンプライアンスのチェック - Azure Security Center
description: チュートリアル:Azure Security Center を使用して規制に対するコンプライアンスを向上させる方法について説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/28/2021
ms.author: memildin
ms.openlocfilehash: 523b081b59bd2f4c45c1ceeb9f39c58f4e3b02b1
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986906"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>チュートリアル:規制に対するコンプライアンスの向上

Azure Security Center では、**規制コンプライアンス ダッシュボード** を使用して、規制に対するコンプライアンス要件を満たすプロセスを効率化できます。 

Security Center では、ハイブリッド クラウド環境の継続的な評価を実行して、サブスクリプションに適用される標準の制御とベスト プラクティスに従ってリスク要因を分析します。 ダッシュボードには、これらの標準へのコンプライアンスの状態が反映されます。 

Azure サブスクリプションで Security Center を有効にすると、[Azure セキュリティ ベンチマーク](../security/benchmarks/introduction.md)が自動的に割り当てられます。 この広く遵守されているベンチマークは、クラウド中心のセキュリティに重点を置いた [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) と [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) の統制に基づいています。

規制コンプライアンス ダッシュボードでは、お客様の環境におけるすべての評価の状態を、特定の標準または規制のコンテキストで把握することができます。 推奨事項に基づいて行動し、お客様の環境内のリスク要因を減らしていくと、コンプライアンス体制は強化されます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 規制コンプライアンス ダッシュボードを使用して規制に対するコンプライアンスを評価する
> * 推奨事項に基づいてアクションを実行することでコンプライアンス体制を強化する

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明されている機能を実行するには、以下が必要です。

- [Azure Defender](azure-defender.md) が有効になっている必要があります。 Azure Defender は 30 日間無料でお試しいただけます。
- ポリシーのコンプライアンス データへの閲覧者アクセス権が付与されたアカウントを使用してサインインする必要があります (**セキュリティ閲覧者** では不十分です)。 サブスクリプションの **グローバル閲覧者** ロールは有効です。 少なくとも、**リソース ポリシーの共同作成者** および **セキュリティ管理者** のロールが割り当てられている必要があります。

##  <a name="assess-your-regulatory-compliance"></a>規制に対するコンプライアンスを評価する

規制コンプライアンス ダッシュボードには、選択したコンプライアンス標準とそのすべての要件が表示されます。サポートされている要件は、適用されるセキュリティ評価に対応付けられています。 これらの評価の状態には、標準へのコンプライアンスが反映されます。

規制コンプライアンス ダッシュボードを使用すると、お客様にとって重要な標準や規制に対するコンプライアンスの不足に焦点を絞ることができます。 また、このように焦点を絞ったビューにより、動的なクラウドおよびハイブリッド環境におけるコンプライアンスを、時間の経過と共に継続的に監視できます。

1. Security Center のメニューから、 **[規制コンプライアンス]** を選択します。

    画面の上部に、コンプライアンスの状態の概要と、サポートされる一連のコンプライアンス規制を示すダッシュボードが表示されます。 お客様の全体的なコンプライアンス スコアのほか、それぞれの標準に関連した合否評価の件数を確認できます。

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="規制コンプライアンス ダッシュボード" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

1. お客様に関連するコンプライアンス標準のタブを選択します (1)。 標準が適用されているサブスクリプション (2) と、その標準に関するすべての制御の一覧 (3) が表示されます。 該当する制御について、その制御に関連付けられた合否評価の詳細 (4) と、影響を受けたリソースの数 (5) を確認できます。 一部の制御は淡色表示されます。それらの制御には、Security Center の評価が関連付けられていません。 これらの要件を確認し、実際の環境でそれらを評価してください。 そうしたものの中には、技術的なものではなくプロセスに関連したものがあります。

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="特定の標準に対するコンプライアンスの詳細を調べる":::

1. 特定の標準について、現在のコンプライアンスの状態をまとめた PDF レポートを生成するには、 **[レポートのダウンロード]** を選択します。

    このレポートは、Security Center の評価データに基づいて、選択した標準のコンプライアンス ステータスの概要を提供し、その特定の標準の制御に従って編成されています。 レポートは直接の利害関係者と共有することができ、内部および外部の監査員に対する証拠を提供するために利用できます。

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="コンプライアンス レポートのダウンロード":::

## <a name="improve-your-compliance-posture"></a>コンプライアンス体制を強化する

規制コンプライアンス ダッシュボードの情報を踏まえ、ダッシュボード内で直接推奨事項を解決することによって、お客様のコンプライアンス体制を強化できます。

1.  ダッシュボードに表示されている評価のうち、不合格になっているものをどれかクリックして、その推奨事項の詳細を表示します。 それぞれの推奨事項には、問題を解決するために従うべき一連の修復手順が含まれています。

1.  特定のリソースを選択すると、そのリソースの情報をさらに詳しく表示して、推奨事項を解決することができます。 <br>たとえば、**Azure CIS 1.1.0** 標準では、**仮想マシンでディスク暗号化を適用する必要がある** という推奨事項を選択できます。

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="標準から推奨事項を選択すると、推奨事項の詳細ページに直接移動します":::

1. この例では、推奨事項の詳細ページから **[アクションの実行]** を選択すると、Azure portal の Azure Virtual Machine ページが表示されます。ここで、 **[セキュリティ]** タブを開いて暗号化を有効にすることができます。

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="推奨事項の詳細ページにある [アクションの実行] ボタンをクリックすると、修復オプションが表示されます":::

    レコメンデーションの適用方法の詳細については、「[Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」を参照してください。

1.  推奨事項を解決するためのアクションを実行すると、お客様のコンプライアンス スコアが向上するので、コンプライアンス ダッシュボード レポートに影響が現れます。

    > [!NOTE]
    > 評価は約 12 時間おきに実行されます。そのため、お客様のコンプライアンス データに影響が現れるのは、関連する評価を次に実行した後に限られます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Security Center の規制コンプライアンス ダッシュボードを使用して以下を行う方法について説明しました。

-   お客様にとって重要な標準および規制を基準にして、お客様のコンプライアンス体制を確認および監視する。
-   関連する推奨事項を解決し、コンプライアンス スコアの向上を確認することで、お客様のコンプライアンス状態を改善する。

規制コンプライアンス ダッシュボードを使用すれば、コンプライアンス プロセスを大幅に簡素化できるほか、お客様の Azure 環境とハイブリッド環境におけるコンプライアンスの証拠を集めるうえで必要な時間を大幅に短縮できます。

詳細については、次の関連記事を参照してください。

-   [規制コンプライアンス ダッシュボード (プレビュー) での動的コンプライアンス パッケージへの更新](update-regulatory-compliance-packages.md) - このプレビュー機能について説明します。これを使用すると、規制コンプライアンス ダッシュボードに表示される標準を新しい "*動的*" パッケージに更新することができます。 また、同じプレビュー機能を使用して、新しいコンプライアンス パッケージを追加したり、その他の標準でコンプライアンスを監視したりすることもできます。 
-   「[Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)」 - Azure リソースの正常性を監視する方法について説明しています。
-   「[Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」 - Azure Security Center の推奨事項をお客様の Azure リソースの保護に役立てる方法について説明しています。
-   「[Azure Security Center 上のセキュリティ スコアの向上](secure-score-security-controls.md)」 - 脆弱性とセキュリティの推奨事項に優先順位を付けて、お客様のセキュリティ体制を最大限に強化する方法について説明しています。
