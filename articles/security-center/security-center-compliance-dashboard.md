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
ms.date: 02/10/2021
ms.author: memildin
ms.openlocfilehash: fb8dc22c923b7b53a6263baa43046862af4d2f04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100370268"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>チュートリアル:規制に対するコンプライアンスの向上

Azure Security Center では、**規制コンプライアンス ダッシュボード** を使用して、規制に対するコンプライアンス要件を満たすプロセスを効率化できます。 

Security Center では、ハイブリッド クラウド環境を継続的に評価して、サブスクリプションに適用される標準の制御とベスト プラクティスに従ってリスク要因を分析します。 ダッシュボードには、これらの標準へのコンプライアンスの状態が反映されます。 

Azure サブスクリプションで Security Center を有効にすると、[Azure セキュリティ ベンチマーク](../security/benchmarks/introduction.md)がそのサブスクリプションに自動的に割り当てられます。 この広く遵守されているベンチマークは、クラウド中心のセキュリティに重点を置いた [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) と [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) の統制に基づいています。

規制コンプライアンス ダッシュボードには、選択した標準または規制について、お客様の環境におけるすべての評価の状態が表示されます。 推奨事項に基づいて行動し、お客様の環境内のリスク要因を減らしていくと、コンプライアンス体制は強化されます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 規制コンプライアンス ダッシュボードを使用して規制に対するコンプライアンスを評価する
> * 推奨事項に基づいてアクションを実行することでコンプライアンス体制を強化する
> * コンプライアンス体制の変化に関するアラートを設定する
> * コンプライアンス データを連続したストリームおよび週単位のスナップショットとしてエクスポートする

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明されている機能を実行するには、以下が必要です。

- [Azure Defender](azure-defender.md) が有効になっている必要があります。 Azure Defender は 30 日間無料でお試しいただけます。
- ポリシーのコンプライアンス データへの閲覧者アクセス権が付与されたアカウントを使用してサインインする必要があります (**セキュリティ閲覧者** では不十分です)。 サブスクリプションの **グローバル閲覧者** ロールは有効です。 少なくとも、**リソース ポリシーの共同作成者** および **セキュリティ管理者** のロールが割り当てられている必要があります。

##  <a name="assess-your-regulatory-compliance"></a>規制に対するコンプライアンスを評価する

規制コンプライアンス ダッシュボードには、選択したコンプライアンス標準とそのすべての要件が表示されます。サポートされている要件は、適用されるセキュリティ評価に対応付けられています。 これらの評価の状態には、標準へのコンプライアンスが反映されます。

規制コンプライアンス ダッシュボードを使用すると、お客様が選択した標準や規制とコンプライアンスのずれに焦点を絞ることができます。 また、このように焦点を絞ったビューにより、動的なクラウドおよびハイブリッド環境におけるコンプライアンスを、時間の経過と共に継続的に監視できます。

1. Security Center のメニューから、 **[規制コンプライアンス]** を選択します。

    画面の上部に、コンプライアンスの状態の概要と、サポートされる一連のコンプライアンス規制を示すダッシュボードが表示されます。 お客様の全体的なコンプライアンス スコアのほか、それぞれの標準に関連した合否評価の件数を確認できます。

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="規制コンプライアンス ダッシュボード" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

1. お客様に関連するコンプライアンス標準のタブを選択します (1)。 標準が適用されているサブスクリプション (2) と、その標準に関するすべての制御の一覧 (3) が表示されます。 該当する制御について、その制御に関連付けられた合否評価の詳細 (4) と、影響を受けたリソースの数 (5) を確認できます。 一部の制御は淡色表示されます。それらの制御には、Security Center の評価が関連付けられていません。 それらの要件を確認し、実際の環境でそれらを評価してください。 そうしたものの中には、技術的なものではなくプロセスに関連したものがあります。

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="特定の標準に対するコンプライアンスの詳細を調べる":::

1. 特定の標準について、現在のコンプライアンスの状態をまとめた PDF レポートを生成するには、 **[レポートのダウンロード]** を選択します。

    このレポートは、Security Center の評価データに基づいて、選択した標準のコンプライアンス状態の概要を提供します。 レポートは、その特定の標準の制御に従って編成されています。 レポートは直接の利害関係者と共有することができ、内部および外部の監査員に対する証拠を提供するために利用できます。

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="コンプライアンス レポートのダウンロード":::

## <a name="improve-your-compliance-posture"></a>コンプライアンス体制を強化する

規制コンプライアンス ダッシュボードの情報を使用して、ダッシュボード内で直接推奨事項を解決することによって、お客様のコンプライアンス体制を強化します。

1.  ダッシュボードに表示されている評価のうち、不合格になっているものをどれか選択して、その推奨事項の詳細を表示します。 それぞれの推奨事項には、問題を解決するための一連の修復手順が含まれています。

1.  特定のリソースを選択すると、そのリソースの情報がさらに詳しく表示され、推奨事項を解決することができます。 <br>たとえば、**Azure CIS 1.1.0** 標準では、**仮想マシンでディスク暗号化を適用する必要がある** という推奨事項を選択できます。

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="標準から推奨事項を選択すると、推奨事項の詳細ページに直接移動します":::

1. この例では、推奨事項の詳細ページから **[アクションの実行]** を選択すると、Azure portal の Azure Virtual Machine ページが表示されます。ここで、 **[セキュリティ]** タブから暗号化を有効にすることができます。

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="推奨事項の詳細ページにある [アクションの実行] ボタンをクリックすると、修復オプションが表示されます":::

    レコメンデーションの適用方法の詳細については、「[Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」を参照してください。

1.  推奨事項を解決するためのアクションを実行すると、お客様のコンプライアンス スコアが向上するので、コンプライアンス ダッシュボード レポートに結果が現れます。

    > [!NOTE]
    > 評価は約 12 時間おきに実行されます。そのため、お客様のコンプライアンス データに影響が現れるのは、関連する評価を次に実行した後に限られます。


## <a name="export-your-compliance-status-data"></a>コンプライアンス状態データをエクスポートする

環境内で他の監視ツールを使用してコンプライアンス状態を追跡する場合、Security Center には、これを容易にするためのエクスポート メカニズムが用意されています。 **連続エクスポート** を構成して、選択したデータを Azure イベント ハブまたは Log Analytics ワークスペースに送信できます。

次のように連続エクスポートを使用して、データを Azure イベント ハブまたは Log Analytics ワークスペースにエクスポートします。

- **連続したストリーム** として、規制コンプライアンス データをエクスポートします。

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-stream.png" alt-text="規制コンプライアンス データのストリームを連続的にエクスポートする" lightbox="media/security-center-compliance-dashboard/export-compliance-data-stream.png":::

- 規制コンプライアンス データの **週単位のスナップショット** をエクスポートします。

    :::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png" alt-text="規制コンプライアンス データの週単位のスナップショットを連続的にエクスポートする" lightbox="media/security-center-compliance-dashboard/export-compliance-data-snapshot.png":::

コンプライアンス データの **PDF または CSV レポート** を、規制コンプライアンス ダッシュボードから直接エクスポートすることもできます。

:::image type="content" source="media/security-center-compliance-dashboard/export-compliance-data-report.png" alt-text="規制コンプライアンス データを PDF または CSV レポートとしてエクスポートする" lightbox="media/security-center-compliance-dashboard/export-compliance-data-report.png":::

詳細については、「[Security Center のデータを連続的にエクスポートする](continuous-export.md)」を参照してください。


## <a name="run-workflow-automations-when-there-are-changes-to-your-compliance"></a>コンプライアンスに変更があったときにワークフロー自動化を実行する

Security Center のワークフロー自動化機能を使用すると、規制コンプライアンス評価のいずれかの状態が変更された場合に Logic Apps をトリガーできます。

たとえば、コンプライアンス評価が失敗したときに Security Center から特定のユーザーにメールが送信されるようにすることができます。 まず、ロジック アプリを作成する必要があります ([Azure Logic Apps](../logic-apps/logic-apps-overview.md) を使用)。次に、「[Security Center のトリガーへの応答を自動化する](workflow-automation.md)」で説明されているように、新しいワークフロー自動化でトリガーを設定します。

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="規制コンプライアンス評価の変更を使用した、ワークフロー自動化のトリガー" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::




## <a name="faq---regulatory-compliance-dashboard"></a>FAQ - 規制コンプライアンス ダッシュボード

- [コンプライアンス ダッシュボードではどの標準がサポートされていますか?](#what-standards-are-supported-in-the-compliance-dashboard)
- [一部の制御が淡色表示されるのはなぜですか?](#why-do-some-controls-appear-grayed-out)
- [PCI DSS、ISO 27001、SOC2 TSP などの組み込みの標準をダッシュボードから削除する方法はありますか?](#how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard)
- [推奨に基づき提案された変更を行いましたが、ダッシュボードにはまだ反映されていない](#i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard)
- [コンプライアンス ダッシュボードにアクセスするには、どのようなアクセス許可が必要ですか?](#what-permissions-do-i-need-to-access-the-compliance-dashboard)
- [規制コンプライアンス ダッシュボードが読み込まれません](#the-regulatory-compliance-dashboard-isnt-loading-for-me)
- [ダッシュボードの標準ごとの成功および失敗した制御を示すレポートを表示する方法はありますか?](#how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard)
- [PDF 以外の形式でコンプライアンス データを含むレポートをダウンロードする方法はありますか?](#how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf)
- [規制コンプライアンス ダッシュボードで一部のポリシーの例外を作成するにはどうすればよいですか?](#how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard)
- [規制コンプライアンス ダッシュボードを使用するには、Azure Defender のプランまたはライセンスが必要ですか?](#what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard)

### <a name="what-standards-are-supported-in-the-compliance-dashboard"></a>コンプライアンス ダッシュボードではどの標準がサポートされていますか?
既定では、規制コンプライアンス ダッシュボードには Azure セキュリティ ベンチマークが表示されます。 Azure セキュリティ ベンチマークは Microsoft が作成したもので、一般的なコンプライアンス フレームワークに基づくセキュリティとコンプライアンスのベスト プラクティスに関する Azure 固有のガイドラインです。 詳細については、「[Azure セキュリティ ベンチマークの概要](../security/benchmarks/introduction.md)」を参照してください。

他の標準へのコンプライアンスを追跡するには、それらをダッシュボードに明示的に追加する必要があります。
 
Azure CIS 1.1.0 (新規)、NIST SP 800-53 R4、NIST SP 800-171 R2、SWIFT CSP CSCF-v2020、UK Official および UK NHS、HIPAA HITRUST、Canada Federal PBMM、ISO 27001、SOC2-TSP、PCI-DSS 3.2.1 などの標準を追加できます。  
 
標準がダッシュボードにさらに追加されると、「[規制コンプライアンス ダッシュボードで標準セットをカスタマイズする](update-regulatory-compliance-packages.md)」に記載されている情報に反映されます。

### <a name="why-do-some-controls-appear-grayed-out"></a>一部の制御が淡色表示されるのはなぜですか?
ダッシュボードのコンプライアンス標準ごとに、標準の制御の一覧が表示されます。 該当する制御について、合否評価の詳細を確認できます。

一部の制御は淡色表示されます。それらの制御には、Security Center の評価が関連付けられていません。 一部はプロシージャまたはプロセスに関連しているため、Security Center では検証することができません。 自動ポリシーや評価がまだ実装されていないものもありますが、今後対応する予定です。 また、一部の制御については、「[クラウドにおける共同責任](../security/fundamentals/shared-responsibility.md)」で説明されているように、プラットフォームに責任があります。 

### <a name="how-can-i-remove-a-built-in-standard-like-pci-dss-iso-27001-or-soc2-tsp-from-the-dashboard"></a>PCI DSS、ISO 27001、SOC2 TSP などの組み込みの標準をダッシュボードから削除する方法はありますか? 
規制コンプライアンス ダッシュボードをカスタマイズし、お客様に該当する標準のみに焦点を当てる場合は、表示されている規制基準のうち、お客様の組織に関連しないものをすべて削除できます。 標準を削除するには、「[ダッシュボードから標準を削除する](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard)」の手順に従ってください。

### <a name="i-made-the-suggested-changed-based-on-the-recommendation-yet-it-isnt-being-reflected-in-the-dashboard"></a>推奨に基づき提案された変更を行いましたが、ダッシュボードにはまだ反映されていない
推奨事項を解決するためのアクションを行った後、コンプライアンス データに変更が反映されていることを確認するには 12 時間お待ちください。 評価は約 12 時間おきに実行されます。そのため、お客様のコンプライアンス データに影響が現れるのは、評価が実行されてからになります。
 
### <a name="what-permissions-do-i-need-to-access-the-compliance-dashboard"></a>コンプライアンス ダッシュボードにアクセスするには、どのようなアクセス許可が必要ですか?
コンプライアンス データを表示するには、少なくとも、ポリシーのコンプライアンス データに対する **閲覧者** アクセス権も必要です。そのため、セキュリティ閲覧者だけでは十分ではありません。 サブスクリプションのグローバル閲覧者である場合は、それでも十分です。

ダッシュボードにアクセスして標準を管理するための最小限のロール セットは、**リソース ポリシー共同作成者** と **セキュリティ管理者** です。


### <a name="the-regulatory-compliance-dashboard-isnt-loading-for-me"></a>規制コンプライアンス ダッシュボードが読み込まれません
規制コンプライアンス ダッシュボードを使用するには、Azure Security Center で Azure Defender がサブスクリプション レベルに対して有効になっている必要があります。 ダッシュボードが正しく読み込まれない場合は、次の手順を試してください。

1. ブラウザーのキャッシュをクリアする。
1. 別のブラウザーを試す。
1. 別のネットワークの場所からダッシュボードを開いてみる。


### <a name="how-can-i-view-a-report-of-passing-and-failing-controls-per-standard-in-my-dashboard"></a>ダッシュボードの標準ごとの成功および失敗した制御を示すレポートを表示する方法はありますか?
メイン ダッシュボードでは、(1) ダッシュボード内でコンプライアンス対応の最も低い標準の "上位 4 件" について成功および失敗した制御のレポートを表示できます。 成功および失敗したすべての制御の状態を表示するには、(2) **[Show all *x*]\(x 件をすべて表示\)** を選択します (x は、追跡している標準の数です)。 コンテキスト プレーンに、追跡しているすべての標準のそれぞれについてコンプライアンス状態が表示されます。

:::image type="content" source="media/security-center-compliance-dashboard/summaries-of-compliance-standards.png" alt-text="規制コンプライアンス ダッシュボードの概要セクション":::


### <a name="how-can-i-download-a-report-with-compliance-data-in-a-format-other-than-pdf"></a>PDF 以外の形式でコンプライアンス データを含むレポートをダウンロードする方法はありますか?
**[レポートのダウンロード]** を選択したら、標準と形式 (PDF または CSV) を選択します。 生成されたレポートには、ポータルのフィルターで選択したサブスクリプションの現在のセットが反映されます。

- PDF レポートには、選択した標準の状態の概要が表示されます
- CSV レポートでは、各制御に関連付けられているポリシーに関連しているため、リソースごとに詳細な結果が提供されます。

現時点では、カスタム ポリシーのレポートのダウンロードはサポートされていません。提供されている規制標準についてのみです。


### <a name="how-can-i-create-exceptions-for-some-of-the-policies-in-the-regulatory-compliance-dashboard"></a>規制コンプライアンス ダッシュボードで一部のポリシーの例外を作成するにはどうすればよいですか?
Security Center に組み込まれ、セキュリティ スコアに含まれるポリシーについては、「[セキュリティ スコアからのリソースと推奨事項の除外](exempt-resource.md)」で説明されているように、ポータルで直接 1 つ以上のリソースの除外を作成できます。

その他のポリシーについては、「[Azure Policy 適用除外の構造](../governance/policy/concepts/exemption-structure.md)」に記載されている手順に従って、除外をポリシー自体で直接作成できます。


### <a name="what-azure-defender-plans-or-licenses-do-i-need-to-use-the-regulatory-compliance-dashboard"></a>規制コンプライアンス ダッシュボードを使用するには、Azure Defender のプランまたはライセンスが必要ですか?
ご利用のいずれかの Azure リソースの種類に対していずれかの Azure Defender パッケージが有効になっている場合は、Security Center で規制コンプライアンス ダッシュボードとそのすべてのデータにアクセスできます。





## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Security Center の規制コンプライアンス ダッシュボードを使用して以下を行う方法について説明しました。

> [!div class="checklist"]
> * お客様にとって重要な標準および規制に関して、お客様のコンプライアンス体制を確認および監視する。
> * 関連する推奨事項を解決し、コンプライアンス スコアの向上を確認することで、お客様のコンプライアンス状態を改善する。

規制コンプライアンス ダッシュボードを使用すると、コンプライアンス プロセスを大幅に簡素化できるほか、お客様の Azure 環境、ハイブリッド環境、およびマルチクラウド環境におけるコンプライアンスの証拠を集めるうえで必要な時間を大幅に短縮できます。

詳細については、次の関連ページを参照してください。

- 「[規制コンプライアンス ダッシュボードでの標準セットのカスタイマイズ](update-regulatory-compliance-packages.md)」 - 規制コンプライアンス ダッシュボードに表示される標準を選択する方法について説明します。 
- [Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md) - Security Center の推奨事項をお客様の Azure リソースの保護に役立てる方法について説明しています。