---
title: チュートリアル - 最適化に関する推奨事項に従って Azure のコストを削減する | Microsoft Docs
description: このチュートリアルは、最適化の推奨事項に従って対処すると、Azure のコスト削減に役立ちます。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/30/2019
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 9306e44655bd172343f20ac4fda2b2c56afcfb88
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164484"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>チュートリアル: 推奨事項に従ってコストを最適化する

Azure Cost Management は Azure Advisor と連携して、コストの最適化に関する推奨事項を提供します。 Azure Advisor を使用すると、活動休止状態のリソースや十分に活用されていないリソースを特定することで効率性を最適化し、改善することができます。 このチュートリアルでは、十分に活用されていない Azure リソースを特定し、コストを削減する措置を取る例を段階的に説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * コストの最適化に関する推奨事項を表示して非効率な可能性がある使用方法を確認する
> * 推奨事項に従って、より費用対効果の高い方法へと仮想マシンのサイズを変更する
> * アクションを検証して仮想マシンが確実にサイズ変更されたことを確認する

## <a name="prerequisites"></a>前提条件
推奨事項は、各種のスコープと Azure アカウント ([Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) のお客様を含む) を対象に公開されています。 サポートされているアカウントの種類の完全な一覧については、「[Understand Cost Management data (Cost Management データの概要)](understand-cost-mgt-data.md)」を参照してください。 コスト データを表示するには、次に示す 1 つ以上のスコープへの読み取りアクセス権が必要です。 スコープの詳細については、「[Understand and work with scopes (スコープを理解して使用する)](understand-work-scopes.md)」を参照してください。

- サブスクリプション
- リソース グループ

14 日間以上のアクティビティがあるアクティブな仮想マシンが必要です。

## <a name="sign-in-to-azure"></a>Azure へのサインイン
Azure Portal ([https://portal.azure.com](https://portal.azure.com/)) にサインインします。

## <a name="view-cost-optimization-recommendations"></a>コストの最適化に関する推奨事項を表示する

サブスクリプションのコスト最適化の推奨事項を表示するには、Azure portal で目的のスコープを開き、 **[Advisor recommendations] \(Advisor の推奨事項\)** を選択します。

管理グループで推奨事項を確認するには、Azure portal で目的のスコープを開き、メニューで **[コスト分析]** を選択します。 別のスコープ (管理グループなど) に切り替えるには、 **[スコープ]** ピルを使用します。 **[Advisor recommendations] \(Advisor の推奨事項\)** を選択します。 スコープの詳細については、「[Understand and work with scopes (スコープを理解して使用する)](understand-work-scopes.md)」を参照してください。

![Azure portal に表示される Cost Management の Advisor の推奨事項](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

推奨事項の一覧には、使用の非効率性が表示されます。また、さらに費用を節約するために役立つ購入に関する推奨事項が表示されます。 合計の **[年間の潜在的な削減額]** には、推奨事項ルールに一致するすべての VM をシャットダウンするか割り当てを解除した場合に節約できる合計額が表示されます。 シャットダウンしたくない場合は、より安価な VM SKU へとサイズを変更することを検討する必要があります。

**[年間の潜在的な削減額]** と共に、 **[影響]** カテゴリは、可能な限り節約できる可能性のある推奨事項を特定するために役立ちます。

影響の大きい推奨事項は次のとおりです。
- [従量課金のコストより費用を節約するために、予約仮想マシン インスタンスを購入する](../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)
- [使用率が低いインスタンスをサイズ変更またはシャットダウンして仮想マシンの支出を最適化する](../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances)
- [マネージド ディスクのスナップショットを格納するために Standard Storage を使用する](../advisor/advisor-cost-recommendations.md#use-standard-snapshots-for-managed-disks)

影響が中程度の推奨事項は次のとおりです。
- [障害が発生している Azure Data Factory パイプラインを削除する](../advisor/advisor-cost-recommendations.md#delete-azure-data-factory-pipelines-that-are-failing)
- [プロビジョニングが解除された ExpressRoute 回線を排除してコストを削減する](../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits)
- [アイドル状態の仮想ネットワーク ゲートウェイを削除または再構成してコストを削減する](../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways)

## <a name="act-on-a-recommendation"></a>推奨事項に従う

Azure Advisor は、仮想マシンの使用状況を 14 日間にわたって監視して、使用率が低い仮想マシンを識別します。 CPU 使用率が 5% 以下で、ネットワークの使用率が 7 MB 以下である日が 4 日以上ある仮想マシンは、使用率が低い仮想マシンと見なされます。

5% 以下の CPU 使用率設定が既定値ですが、この設定は調整できます。 設定の調整について詳しくは、[使用率の低い仮想マシンの推奨事項について平均 CPU 使用率ルールを構成する](../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation)方法についての記事をご覧ください。

設計によっては使用率が低くなるシナリオもありますが、多くの場合、仮想マシンのサイズをより安価なサイズに変更することでコストを削減できます。 サイズ変更アクションを選択した場合の実際の節約額は異なる可能性があります。 それでは仮想マシンのサイズ変更の例を見てみましょう。

推奨事項の一覧から **[使用率が低い仮想マシンを適切なサイズに変更するかシャットダウンしてください]** の推奨事項をクリックします。 仮想マシンの候補一覧からサイズを変更する仮想マシンを選択してから、仮想マシンをクリックします。 仮想マシンの詳細が表示されるので、使用率のメトリックを確認できます。 **[年間の潜在的な削減額]** 値は、その VM をシャットダウンまたは削除した場合に節約できる額です。 VM のサイズを変更するとコストを節約できる可能性がありますが、年間の潜在的な削減額の全額は節約されません。

![推奨事項の詳細の例](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

VM の詳細で、仮想マシンの使用率を確認して、適切なサイズ変更の候補であることを確認します。

![履歴の使用率を示す VM の詳細の例](./media/tutorial-acm-opt-recommendations/vm-details.png)

現在の仮想マシンのサイズをメモします。 仮想マシンのサイズを変更する必要があることを確認したら、仮想マシンの詳細を閉じて仮想マシンの一覧を表示します。

シャットダウンまたはサイズを変更する候補の一覧から **[仮想マシンのサイズ変更]** を選択します。
![仮想マシンのサイズを変更するオプションを伴う推奨事項の例](./media/tutorial-acm-opt-recommendations/resize-vm.png)

次に、使用できるサイズ変更オプションの一覧が表示されます。 実際にシナリオに対応するベスト プラクティスと費用対効果を実現するものを選択します。 次の例で、選択されているオプションは **DS14\_V2** から **DS13\_V2** へのサイズ変更です。 この推奨事項に従うと、551.30 ドル/月または 6,615.60 ドル/年を節約できます。

![サイズを選択できる使用可能な VM サイズの一覧の例](./media/tutorial-acm-opt-recommendations/choose-size.png)

適切なサイズを選択したら、 **[選択]** をクリックしてサイズ変更アクションを開始します。

サイズ変更には、アクティブに実行されている仮想マシンを再起動する必要があります。 仮想マシンが運用環境内にある場合は、営業時間後にサイズ変更操作を実行することをお勧めします。 再起動のスケジュールを設定すると、一時的な使用不能による中断を減らすことができます。

## <a name="verify-the-action"></a>アクションを検証する

VM のサイズ変更が正常に完了したら、Azure の通知が表示されます。

![正常にサイズ変更された仮想マシンの通知](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * コストの最適化に関する推奨事項を表示して非効率な可能性がある使用方法を確認する
> * 推奨事項に従って、より費用対効果の高い方法へと仮想マシンのサイズを変更する
> * アクションを検証して仮想マシンが確実にサイズ変更されたことを確認する

Cost Management のベスト プラクティスに関する記事をまだ読んでいない場合は、コストの管理に役立つ高度なガイダンスと原則が記載されている記事をお読みください。

> [!div class="nextstepaction"]
> [Cost Management のベスト プラクティス](cost-mgt-best-practices.md)
