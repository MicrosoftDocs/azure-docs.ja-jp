---
title: コストの計画と管理
titleSuffix: Azure Machine Learning
description: Azure portal のコスト分析を使用して、Azure Machine Learning のコストを計画および管理します。 機械学習モデルを構築するときは、コストを下げるためのコスト節約に関するヒントをさらに学習します。
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 2fc9a1a1c3a08f0530649ae64926c673e2d666e0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87012690"
---
# <a name="plan-and-manage-costs-for-azure-machine-learning"></a>Azure Machine Learning のコストを計画して管理する

この記事では、Azure Machine Learning のコストを計画して管理する方法について説明します。 リソースを追加する前に、まず、コストの計画に役立つ Azure 料金計算ツールを使用します。 次に、Azure リソースを追加するときに、推定コストを確認します。 最後に、マネージド Azure Machine Learning コンピューティング クラスターでモデルをトレーニングするときに、コスト節約のヒントを使用します。

Azure Machine Learning リソースを使い始めた後は、コスト管理機能を使用して、予算の設定とコストの監視を行います。 また、予想コストを確認し、支出の傾向を明らかにして、対処した方がよい部分を識別します。

Azure Machine Learning のコストは、Azure の月額請求料金の一部でしかないことを理解してください。 Azure の他のサービスを使用している場合は、サードパーティのサービスを含め、お使いの Azure サブスクリプションで使用されている Azure のすべてのサービスとリソースに対して課金されます。 この記事では、Azure Machine Learning のコストを計画および管理する方法について説明します。 Azure Machine Learning のコスト管理に慣れたら、同様の方法を適用して、サブスクリプションで使用されているすべての Azure サービスのコストを管理します。

機械学習モデルをトレーニングするときは、マネージド Azure Machine Learning コンピューティング クラスターを使用して、さらにコストを節約するためのヒントを活用します。

* 自動スケーリングされるようにトレーニング クラスターを構成する
* サブスクリプションとワークスペースにクォータを設定する
* トレーニングの実行に終了ポリシーを設定する
* 優先順位の低い仮想マシン (VM) を使用する
* Azure 予約 VM インスタンスを使用する

## <a name="prerequisites"></a>前提条件

コスト分析では、さまざまな種類の Azure アカウントがサポートされています。 サポートされているアカウントの種類の完全な一覧については、「[Understand Cost Management data (Cost Management データの概要)](../cost-management-billing/costs/understand-cost-mgt-data.md)」を参照してください。 コスト データを表示するには、少なくとも Azure アカウントの読み取りアクセス許可が必要です。 

Azure Cost Management データに対するアクセス権の割り当てについては、[データへのアクセス許可の割り当て](../cost-management-billing/costs/assign-access-acm-data.md)に関するページを参照してください。

## <a name="estimate-costs"></a>コストの見積もり

Azure Machine Learning アカウントにリソースを作成する前に、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用してコストを見積もります。 左側で、 **[AI + 機械学習]** を選択してから、 **[Azure Machine Learning]** を選択して始めます。  

次のスクリーンショットでは、計算ツールを使用したコストの見積もりを示します。

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Azure 料金計算ツールでのコスト見積り":::

ワークスペースに新しいリソースを追加したら、この計算ツールに戻り、同じリソースをここに追加して、コストの見積もりを更新します。

Enterprise エディションがプレビュー段階にある間、ML の追加料金は発生しません。 Enterprise エディションが一般提供されるようになると、(トレーニングと推論のための) 追加料金が発生します。  詳細については、「[Azure Machine Learning の価格](https://azure.microsoft.com/pricing/details/machine-learning/)」を参照してください。

## <a name="get-cost-alerts"></a>コストのアラートを受け取る

[予算](../cost-management/tutorial-acm-create-budgets.md)を作成してコストを管理し、異常な支出や超過出費のリスクについて関係者に自動的に通知する[アラート](../cost-management/cost-mgt-alerts-monitor-usage-spending.md)を作成します。 アラートは、予算とコストのしきい値と比較した支出に基づきます。 予算とアラートは、Azure サブスクリプションとリソース グループに対して作成されるため、全体的なコスト監視戦略の一環として役立ちます。 ただし、予算とアラートは、概要レベルでコストを追跡するように設計されているため、個々の Azure サービスのコストを管理する機能は限定されていることがあります。

## <a name="monitor-costs"></a>コストを監視する

Azure Machine Learning でリソースを使用すると、コストが発生します。 Azure リソースの使用のユニット コストは、期間 (秒、分、時間、日) や要求ユニット使用量によって異なります。 Azure Machine Learning を使い始めるとすぐにコストが発生します。 これらのコストは、Azure portal の [[コスト分析]](../cost-management/quick-acm-cost-analysis.md) ペインに表示されます。

さまざまな時間間隔でグラフとテーブルにコストを表示できます。 予算や予想コストを基準としてコストを表示することもできます。 より長い期間の時系列ビューに切り替えると、支出の傾向を特定し、超過出費が発生した可能性のある時期を確認するのに役立ちます。 予算を作成したら、それを超えた箇所を確認します。  

Machine Learning の個別のサービス領域については表示されません。  代わりに、Machine Learning ワークスペースに追加したさまざまなリソースについて表示されます。

## <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Azure Machine Learning コンピューティング クラスター (AmlCompute) を使用する

データは絶えず変化するので、モデルのトレーニングと再トレーニングを短時間で実行できるように合理化し、正確なモデルを維持する必要があります。 ただし、継続的にトレーニングを行うとコストがかかり、GPU 上でのディープ ラーニング モデルの場合は特にそうです。 

Azure Machine Learning のユーザーは、マネージド Azure Machine Learning コンピューティング クラスター (AmlCompute とも呼ばれます) を使用できます。 AmlCompute では、GPU と CPU のさまざまなオプションがサポートされています。 AmlCompute は、Azure Machine Learning によってサブスクリプションの代わりに内部でホストされます。 Azure IaaS クラウド スケールで、同じエンタープライズ レベルのセキュリティ、コンプライアンス、ガバナンスが提供されます。

これらのコンピューティング プールは Azure の IaaS インフラストラクチャ内にあるため、インフラストラクチャの他の部分と同じセキュリティ要件とコンプライアンス要件で、トレーニングをデプロイ、スケーリング、管理できます。  これらのデプロイは、お客様のサブスクリプションで実行され、ガバナンス ルールに従います。 詳しくは、「[Azure Machine Learning コンピューティング](how-to-set-up-training-targets.md#amlcompute)」をご覧ください。

## <a name="configure-training-clusters-for-autoscaling"></a>自動スケーリングされるようにトレーニング クラスターを構成する

ワークロードの要件に基づいてクラスターを自動スケーリングすると、必要なものだけが使用されるようにして、コストを削減できます。

AmlCompute クラスターは、ワークロードに基づいて動的にスケーリングするように設計されています。 クラスターは、構成するノードの最大数までスケールアップできます。 実行が完了するたびに、クラスターによってノードが解放され、構成されている最小ノード数にスケーリングされます。

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

また、スケールダウンまでのノードのアイドル時間を構成することもできます。 既定では、スケールダウンまでのアイドル時間は 120 秒に設定されます。

+ 反復の少ない実験を実行する場合は、この時間を短くしてコストを節約します。
+ 反復の多い開発またはテストの実験を実行する場合は、トレーニング スクリプトまたは環境を変更するたびに、常にスケールアップとスケールダウンの料金が発生しないように、時間を長くする必要がある場合があります。

AmlCompute クラスターは、[AmlCompute SDK クラス](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py)、[AmlCompute CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)、[REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable) を使用して、Azure portal の変化するワークロード要件に合わせて構成できます。

```azure cli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

## <a name="set-quotas-on-resources"></a>リソースにクォータを設定する

AmlCompute には、[クォータ (制限) の構成](how-to-manage-quotas.md#azure-machine-learning-compute)が付随します。 このクォータは VM ファミリ (たとえば、Dv2 シリーズ、NCv3 シリーズ) 別であり、各サブスクリプションのリージョンによって異なります。 サブスクリプションは小さい既定値で開始されますが、この設定を使用して、サブスクリプションでのスピンアップに使用できる AmlCompute リソースの量を制御できます。 

また、サブスクリプション内の各ワークスペースに対し、[VM ファミリ別のワークスペース レベル クォータ](how-to-manage-quotas.md#workspace-level-quota)を構成します。 それにより、各ワークスペースで発生する可能性があるコストをより細かく制御し、特定の VM ファミリを制限できます。 

ワークスペース レベルでクォータを設定するには、[Azure portal](https://portal.azure.com) で始めます。  サブスクリプション内の任意のワークスペースを選択し、左側のペインで **[Usages + quotas]\(使用量 + クォータ\)** を選択します。 次に、 **[クォータの構成]** タブを選択して、クォータを表示します。 このクォータは、複数のワークスペースに影響する設定であるため、設定するにはサブスクリプション スコープの特権が必要です。

## <a name="set-run-autotermination-policies"></a>実行の自動終了ポリシーを設定する 

場合によっては、期間を制限したり、早期に終了したりするようにトレーニングの実行を構成する必要があります。 たとえば、Azure Machine Learning の組み込みのハイパーパラメーターのチューニングや自動機械学習を使用している場合などです。

使用できるオプションを次に示します。
* 選択したコンピューティング (ローカルまたはリモートのクラウド コンピューティング) で実行を延長できる最長期間を制御するには、RunConfiguration で `max_run_duration_seconds` という名前のパラメーターを定義します。
* [ハイパーパラメーターのチューニング](how-to-tune-hyperparameters.md#early-termination)の場合は、バンディット ポリシー、中央値の停止ポリシー、または切り捨て選択ポリシーから、早期終了ポリシーを定義します。 ハイパーパラメーター スイープをさらに制御するには、`max_total_runs` や `max_duration_minutes` などのパラメーターを使用します。
* [自動機械学習](how-to-configure-auto-train.md#exit)の場合は、`enable_early_stopping` フラグを使用して同様の終了ポリシーを設定します。 また、`iteration_timeout_minutes` や `experiment_timeout_minutes` などのプロパティを使用して、実行や実験全体の最長期間を制御します。

## <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> 優先順位の低い VM を使用する

Azure では、仮想マシン スケール セット、Batch、および Machine Learning service 間で、過剰な未使用容量を低優先順位の VM として使用できます。 これらの割り当ては先取り可能ですが、専用の VM と比較して低価格で提供されます。 一般に、Batch ワークロードには優先順位の低い VM を使用することをお勧めします。 また、再送信 (Batch 推論の場合) または再起動 (チェックポイントを使用したディープ ラーニング トレーニングの場合) によって中断を回復できる場合にもそれらを使用します。

低優先順位の VM には、専用のクォータ値とは別に、VM ファミリ別の 1 つのクォータがあります。 詳しくは、[AmlCompute のクォータ](how-to-manage-quotas.md)に関する記事をご覧ください。

 コンピューティング インスタンスは、対話型のノートブック エクスペリエンスをサポートする必要があるため、低優先順位の VM では機能しません。

## <a name="use-reserved-instances"></a>予約インスタンスを使用する

コンピューティング リソースのコストを削減するもう 1 つの方法は、Azure 予約 VM インスタンスです。 このオファリングでは、1 年間または 3 年間の契約を結びます。 これらの割引は、従量課金制料金の最大 72% までの範囲で、毎月の Azure の請求に直接適用されます。

Azure Machine Learning コンピューティングでは、予約インスタンスがもともとサポートされています。 1 年間または 3 年間の予約インスタンスを購入すると、Azure Machine Learning のマネージド コンピューティングに自動的に割引が適用されます。


## <a name="next-steps"></a>次のステップ

各項目の詳細情報
* [リソース クォータの管理と増加](how-to-manage-quotas.md)
* [コスト分析を使用したコストの管理](../cost-management-billing/costs/quick-acm-cost-analysis.md)。
* [Azure Machine Learning コンピューティング](how-to-set-up-training-targets.md#amlcompute)
