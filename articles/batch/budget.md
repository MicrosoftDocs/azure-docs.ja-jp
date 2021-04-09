---
title: Azure Batch のコスト分析の取得と予算の設定
description: Batch ワークロードの実行に使用される、基になるコンピューティング リソースとソフトウェア ライセンスについて、コスト分析を取得して予算を設定し、コストを削減する方法について説明します。
ms.topic: how-to
ms.date: 01/29/2021
ms.openlocfilehash: d1fc2d15a7037e56a8056efa67d2017badb77ffd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99091329"
---
# <a name="get-cost-analysis-and-set-budgets-for-azure-batch"></a>Azure Batch のコスト分析の取得と予算の設定

このトピックは、Azure Batch に関連する可能性があるコスト、Batch プールまたはアカウントの予算の設定方法、Batch ワークロードのコストを削減する方法を理解するのに役立ちます。

## <a name="understand-costs-associated-with-batch-resources"></a>Batch リソースに関連するコストを把握する

Azure Batch 自体の使用にコストはかかりませんが、Batch ワークロードの実行に使用される、基になるコンピューティング リソースとソフトウェア ライセンスには料金が発生する場合があります。 コストは、プール内の仮想マシン (VM)、VM からのデータ転送、またはクラウドに格納されている任意の入力または出力データから発生します。

### <a name="virtual-machines"></a>仮想マシン

仮想マシンは、Batch 処理に使用される最も重要なリソースです。 Batch に VM を使用するコストは、使用の種類、量、および期間に基づいて計算されます。 VM の課金オプションには、[従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/)または[予約](../cost-management-billing/reservations/save-compute-costs-reservations.md) (前払い) があります。 どちらの支払いオプションにも、コンピューティング ワークロードに応じてさまざまなメリットがあり、これらのオプションが請求に与える影響は異なります。

[仮想マシンの構成](nodes-and-pools.md#virtual-machine-configuration)を使用して作成されたプール内の各 VM には、Azure マネージド ディスクを使用する関連 OS ディスクがあります。 Azure マネージド ディスクには追加のコストがかかり、他のディスク パフォーマンス層にも異なるコストがあります。

### <a name="storage"></a>ストレージ

[アプリケーション パッケージ](batch-application-packages.md)を使用してアプリケーションを Batch ノード (VM) デプロイすると、アプリケーション パッケージが使用する Azure Storage リソースに対して課金されます。 また、リソース ファイルやその他のログ データなどの入力または出力ファイルのストレージに対しても課金されます。

一般に、Batch に関連付けられているストレージ データのコストは、コンピューティング リソースのコストよりはるかに低くなります。

### <a name="networking-resources"></a>ネットワーク リソース

Batch プールにはネットワーク リソースが使用されますが、その一部には関連コストが発生します。 特に、仮想マシン構成プールの場合は、静的 IP アドレスを必要とする Standard ロード バランサーが使用されます。 Batch によって使用されるロード バランサーは、ユーザー サブスクリプション モードで構成されている[アカウント](accounts.md#batch-accounts)では表示されますが、Batch サービス モードのものでは表示されません。

Standard ロード バランサーでは、Batch プール VM との間でやり取りされるすべてのデータに対して料金が発生します。 プール ノード (タスクまたはノード ファイルの取得など)、タスク アプリケーション パッケージ、リソースまたは出力ファイル、およびコンテナー イメージからデータを取得する一部の Batch API にも料金が発生します。

### <a name="additional-services"></a>その他のサービス

お客様が Batch ソリューションと共に使用するサービスによっては、追加料金が発生する可能性があります。 各追加サービスのコストを確認するには、「[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)」を参照してください。 関連コストが発生する可能性のある Batch で一般的に使用されるサービスには、次のものがあります。

- Application Insights
- Data Factory
- Azure Monitor
- Virtual Network
- グラフィック アプリケーションを使用する VM

## <a name="view-cost-analysis-and-create-a-budget-for-a-pool"></a>コスト分析を表示してプールの予算を作成する

Azure portal で、Batch プールまたは Batch アカウントの予算と支出アラートを作成できます。 予算とアラートは、予算オーバーのリスクを関係者に通知するのに役立ちますが、支出アラートの遅延が生じたり、予算をわずかに超過したりする危険性があります。

> [!NOTE]
> この例のプールでは **仮想マシンの構成** を使用します。これはほとんどのプールで推奨されており、Virtual Machines の料金体系に基づいて課金されます。 **Cloud Services 構成** を使用するプールは、Cloud Services の料金体系に基づいて課金されます。

### <a name="view-cost-analysis-for-a-batch-pool"></a>Batch プールのコスト分析を表示する

1. Azure portal で、**Cost Management + Billing** を入力するか、選択します。
1. **[課金スコープ]** セクションでご使用のサブスクリプションを選択します。
1. **[コスト管理]** で、 **[コスト分析]** を選択します。
1. **[フィルターの追加]** を選択します 最初のドロップダウンで、 **[リソース]** を選択します
1. 2 番目のドロップダウンで、Batch プールを選択します。 プールを選択すると、次に示す例のようなプールのコスト分析が表示されます。
    ![Azure portal のプールのコスト分析を示すスクリーンショット。](./media/batch-budget/pool-cost-analysis.png)

結果のコスト分析には、プールのコストと、そのコストの原因であるリソースが表示されます。 この例では、プールで使用されている VM が最もコストのかかるリソースです。

### <a name="create-a-budget-for-a-batch-pool"></a>Batch プールの予算を作成する

1. **[コスト分析]** ページで、 **[Budget: none]\(予算: なし\)** を選択します。
1. **[新しい予算の作成 >]** を選択します。
1. 表示されたウィンドウを使用して、お使いのプールに合わせて予算を構成します。 詳細については、[Azure の予算を作成して管理する](../cost-management-billing/costs/tutorial-acm-create-budgets.md)」を参照してください。

## <a name="minimize-costs-associated-with-azure-batch"></a>Azure Batch に関連するコストを最小限に抑える

シナリオによっては、可能な限りコストを削減することが必要になる場合があります。 ワークロードの効率を最大化し、潜在的なコストを削減するには、これらの 1 つ以上の戦略の使用を検討してください。

### <a name="use-low-priority-virtual-machines"></a>優先順位の低い仮想マシンを使用する

[優先順位の低い VM](batch-low-pri-vms.md) を使用すると、Azure の余剰コンピューティング容量が利用されるので、Batch ワークロードのコストが削減されます。 プールで優先順位の低い VM を指定すると、Batch ではこの余剰を使用してワークロードが実行されます。 専用 VM の代わりに優先順位の低い VM を使用すると、コストを大幅に節約できます。

### <a name="select-a-standard-virtual-machine-os-disk-type"></a>Standard の仮想マシン OS ディスクの種類を選択する

Azure には、[VM OS ディスクの種類](../virtual-machines/disks-types.md)が複数用意されています。 ほとんどの VM シリーズには、Premium ストレージと Standard ストレージの両方をサポートするサイズがあります。 プールに VM サイズとして 's' が選択されている場合、Batch では Premium SSD OS ディスクが構成されます。 's 以外' の VM サイズを選択すると、より低コストの Standard HDD ディスクの種類が使用されます。 たとえば、Premium SSD OS ディスクは `Standard_D2s_v3` に使用され、Standard HDD OS ディスクは `Standard_D2_v3` に使用されます。

Premium SSD OS ディスクはより高価ですが、高いパフォーマンスが得られます。 Premium ディスクを使用する VM は、Standard HDD OS ディスクを使用する VM よりも多少速く起動できます。 アプリケーションとタスク ファイルは VM の一時 SSD ディスク上にあるため、Batch では、多くの場合、OS ディスクはそれほど頻繁に使用されません。 このため、多くの場合、's 以外' の VM サイズを選択して、's' の VM サイズを指定したときにプロビジョニングされる Premium SSD の増加コストの支払を回避することができます。

### <a name="purchase-reservations-for-virtual-machine-instances"></a>仮想マシン インスタンスの予約を購入する

長期間 Batch を使用する予定であれば、ワークロードに [Azure の予約](../cost-management-billing/reservations/save-compute-costs-reservations.md)を使用することで VM のコストを削減できます。 予約料金は、従量課金制の料金よりもかなり低価です。 予約なしで使用された仮想マシン インスタンスは、従量課金制で料金が発生します。 予約を購入すると、予約割引が適用されます。

### <a name="use-automatic-scaling"></a>自動スケーリングを使用する

[自動スケーリング](batch-automatic-scaling.md)では、現在のジョブの要求に基づいて、Batch プール内の VM 数が動的にスケーリングされます。 自動スケーリングでは、ジョブの有効期間に基づいてプールがスケールされるので、実行するジョブがある場合にのみ VM がスケールアップされ、使用されます。 ジョブが完了したとき、またはジョブがないときは、コンピューティング リソースを節約するために VM は自動的にスケールダウンされます。 スケーリングを使用すると、必要なリソースのみを使用して Batch ソリューションの全体的なコストを削減できます。

## <a name="next-steps"></a>次のステップ

- [Azure Cost Management および Billing](../cost-management-billing/cost-management-billing-overview.md) の詳細について確認します。
- [Batch での優先順位の低い VM の使用](batch-low-pri-vms.md)について確認します。
