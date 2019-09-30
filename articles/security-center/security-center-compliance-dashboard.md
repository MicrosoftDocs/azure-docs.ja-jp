---
title: Azure Security Center を使用して規制に対するコンプライアンスを向上させる | Microsoft Docs
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
ms.date: 4/30/2019
ms.author: memildin
ms.openlocfilehash: 20842997c5df81835024a6f458cd863b4e4d78b0
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202427"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>チュートリアル:規制に対するコンプライアンスの向上
---

Azure Security Center では、[Regulatory compliance]\(規制に対するコンプライアンス\) ダッシュボードを使用して、規制に対するコンプライアンス要件を満たすプロセスを効率化できます。 このダッシュボードでは、お客様の Azure 環境の継続的評価に基づき、お客様のコンプライアンス体制についての分析情報が Security Center から得られます。 Security Center によって実施される評価では、お客様のハイブリッド クラウド環境におけるリスク要因が、セキュリティのベスト プラクティスに従って分析されます。 それらの評価は、サポートされている一連の標準のコンプライアンス制御に対応付けられます。 [Regulatory compliance]\(規制に対するコンプライアンス\) ダッシュボードでは、お客様の環境におけるすべての評価の状態を、特定の標準または規制のコンテキストで明確に把握することができます。 推奨事項に基づいて行動し、お客様の環境内のリスク要因を減らしていく過程で、お客様のコンプライアンス体制が強化されていくことに気付くでしょう。

このチュートリアルで学習する内容は次のとおりです。

-   [Regulatory compliance]\(規制に対するコンプライアンス\) ダッシュボードを使用して規制に対するコンプライアンスを評価する

-   推奨事項に基づいてアクションを実行することでコンプライアンス体制を強化する

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明されている機能を実行するには、Security Center の Standard 価格レベルが必要です。 Security Center Standard は無料でお試しいただけます。
詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/security-center/)を参照してください。 Standard にアップグレードする方法については、[Azure サブスクリプションでの Security Center Standard の利用開始](https://docs.microsoft.com/azure/security-center/security-center-get-started)に関するクイックスタートを参照してください。

##  <a name="assess-your-regulatory-compliance"></a>規制に対するコンプライアンスを評価する

Security Center では、お客様のリソースの構成を継続的に評価して、セキュリティの問題と脆弱性を特定します。 これらの評価は、お客様のセキュリティ検疫の強化に向けた推奨事項として表示されます。 [Regulatory compliance]\(規制に対するコンプライアンス\) ダッシュボードでは、一連のコンプライアンス標準とそれらのすべての要件を確認できます。ここでは、サポートされている要件が適切なセキュリティ評価に対応付けられています。 そのため、これらの評価の状態に基づき、標準と照らし合わせてお客様のコンプライアンス体制を確認することができます。

[Regulatory compliance]\(規制に対するコンプライアンス\) ダッシュボードのビューでは、お客様にとって重要な標準または規制に対するコンプライアンスの不足に焦点を絞ることができます。 また、このように焦点を絞ったビューによって、クラウドとハイブリッドの動的環境におけるお客様のコンプライアンス スコアを、時間の経過と共に継続的に監視することができます。

>[!NOTE]
> 現在サポートされている規制標準は Azure CIS、PCI DSS 3.2、ISO 27001、SOC TSP です。 その他の標準は、開発が進むにつれてダッシュボードに追加される予定です。
1.  Security Center のメイン メニューの **[ポリシーとコンプライアンス]** で、 **[規制コンプライアンス]** を選択します。 <br>
画面の上部に、お客様のコンプライアンス状態の概要とサポートされる一連のコンプライアンス規制が記載されたダッシュボードが表示されます。 お客様の全体的なコンプライアンス スコアのほか、それぞれの標準に関連した合否評価の件数を確認できます。

    ![コンピューターの説明 (高い信頼度)](./media/security-center-compliance-dashboard/compliance-dashboard.png)


2.  お客様に関連するコンプライアンス標準のタブを選択します。 その標準のすべての制御が一覧表示されます。 該当する制御について、その制御に関連付けられた合否評価の詳細を確認できます。 一部の制御は淡色表示されます。それらの制御には、Security Center の評価が関連付けられていません。 それらについては要件をご自身で分析し、お客様の環境で評価する必要があります。 そうしたものの中には、技術的なものではなくプロセスに関連したものがあります。

    ![コンプライアンス タブ](./media/security-center-compliance-dashboard/compliance-pci.png)

3. **[すべて]** タブを選択すると、関連する Security Center の全推奨事項とそれらに関連付けられた標準が表示されます。 このビューは、特定の推奨事項の影響を受けるさまざまな標準をすべて特定する際に役立ちます。 <br> このビューを使用して、解決が必要な推奨事項に優先順位を付けることもできます。 たとえば、 **[サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする]** という推奨事項が複数のリソースで不合格になっていて、なおかつ複数の標準に関連付けられていることがわかった場合、その推奨事項の解決は、お客様の全体的なコンプライアンス スコアに大きく影響することになります。

    ![コンプライアンス スコアの影響](./media/security-center-compliance-dashboard/compliance-all-tabs.png)

1. 特定の標準についての現在のコンプライアンス ステータスを要約した PDF レポートをダウンロードするには、 **[レポートのダウンロード]** をクリックします。

    このレポートは、Security Center の評価データに基づいて、選択した標準のコンプライアンス ステータスの概要を提供し、その特定の標準の制御に従って編成されています。 レポートは直接の利害関係者と共有することができ、内部および外部の監査員に対して証拠を提供するために利用できます。

    ![ダウンロード](./media/security-center-compliance-dashboard/download-report.png)

## <a name="improve-your-compliance-posture"></a>コンプライアンス体制を強化する

[Regulatory compliance]\(規制に対するコンプライアンス\) ダッシュボードの情報を踏まえ、ダッシュボード内で直接推奨事項を解決することによって、お客様のコンプライアンス体制を強化できます。

1.  ダッシュボードに表示されている評価のうち、不合格になっているものをどれかクリックして、その推奨事項の詳細を表示します。 それぞれの推奨事項には、問題を解決するために従うべき一連の修復手順が含まれています。

2.  特定のリソースを選択すると、そのリソースの情報をさらに詳しく表示して、推奨事項を解決することができます。 <br>たとえば、**Azure CIS 標準**のタブで、 **[ストレージ アカウントへの安全な転送を必須にする]** という推奨事項をクリックすることができます。

    ![コンプライアンスに関する推奨事項](./media/security-center-compliance-dashboard/compliance-recommendation.png)

3. 推奨事項の情報をクリックして異常なリソースを選択すると、Azure portal 内で**安全なストレージ転送**を有効にするためのエクスペリエンスに直接誘導されます。<br>レコメンデーションの適用方法の詳細については、「[Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」を参照してください。

    ![コンプライアンスに関する推奨事項](./media/security-center-compliance-dashboard/compliance-remediate-recommendation.png)

4.  推奨事項を解決するためのアクションを実行すると、お客様のコンプライアンス スコアが向上するので、コンプライアンス ダッシュボード レポートに影響が現れます。

    > [!NOTE]
    > 評価は約 12 時間おきに実行されます。そのため、お客様のコンプライアンス データに影響が現れるのは、評価が実行されてからになります。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Security Center の [Regulatory compliance]\(規制に対するコンプライアンス\) ダッシュボードを使用して以下を行う方法について説明しました。

-   お客様にとって重要な標準および規制を基準にして、お客様のコンプライアンス体制を確認および監視する。

-   関連する推奨事項を解決し、コンプライアンス スコアの向上を確認することで、お客様のコンプライアンス状態を改善する。

[Regulatory compliance]\(規制に対するコンプライアンス\) ダッシュボードを使用すれば、コンプライアンス プロセスを大幅に簡素化できるほか、お客様の Azure 環境とハイブリッド環境におけるコンプライアンスの証拠を集めるうえで必要な時間を大幅に短縮できます。

Security Center の詳細については、次の記事をご覧ください。

-   「[Azure セキュリティ センターでのセキュリティ ヘルスの監視](security-center-monitoring.md)」-- Azure リソースの正常性を監視する方法について説明しています。

-   「[Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」-- Azure Security Center の推奨事項をお客様の Azure リソースの保護に役立てる方法について説明しています。

-   「[Azure Security Center 上のセキュリティ スコアの向上](security-center-secure-score.md)」-- 脆弱性とセキュリティの推奨事項に優先順位を付けて、お客様のセキュリティ体制を最大限に強化する方法について説明しています。

-   「[Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)」-- このサービスの使用に関してよく寄せられる質問が記載されています。
