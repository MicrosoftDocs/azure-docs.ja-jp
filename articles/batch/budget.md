---
title: コスト分析と予算 - Azure Batch
description: コスト分析を取得し、Batch ワークロードの予算を設定する方法について説明します。
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 07/19/2019
ms.author: lahugh
ms.openlocfilehash: 6ccf530fe2164b3d9b1936648ffe9057c334efd6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094202"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Azure Batch のコスト分析と予算

Azure Batch 自体に料金はかかりません。Batch ワークロードの実行に使用される基のコンピューティング リソースとソフトウェア ライセンスにのみかかります。 概要としては、プール内の仮想マシン (VM)、VM からのデータ転送、またはクラウドに格納されている任意の入力または出力データからコストが発生します。 Batch のいくつかの主要コンポーネントを見て、コストが発生する箇所、プールまたはアカウントの予算を設定する方法、および Batch のワークロードのコスト効率をさらに高めるいくつかの手法を理解しましょう。

## <a name="batch-resources"></a>Batch リソース

仮想マシンは、Batch 処理に使用される最も重要なリソースです。 Batch に VM を使用するコストは、使用の種類、量、および期間に基づいて計算されます。 VM の課金オプションには、[従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/)または[予約](../billing/billing-save-compute-costs-reservations.md) (前払い) があります。 どちらの支払いオプションにも、コンピューティング ワークロードに応じてさまざまなメリットがあります。また、両方の支払いモデルが請求に与える影響は異なります。

[アプリケーション パッケージ](batch-application-packages.md)を使用してアプリケーションを Batch ノード (VM) デプロイすると、アプリケーション パッケージが使用する Azure Storage リソースに対して課金されます。 また、リソース ファイルやその他のログデータなどの入力ファイルまたは出力ファイルのストレージに対しても課金されます。 一般に、Batch に関連付けられているストレージ データのコストは、コンピューティング リソースのコストよりはるかに低くなります。 **VirtualMachineConfiguration** を使用して作成されたプール内の各 VM には、Azure マネージド ディスクを使用する関連 OS ディスクがあります。 Azure マネージド ディスクには追加のコストがかかり、他のディスク パフォーマンス層にも異なるコストがあります。

Batch プールにはネットワーク リソースが使用されます。 特に、**VirtualMachineConfiguration** プールの場合は、静的 IP アドレスを必要とする Standard ロード バランサーが使用されます。 Batch に使用されるロード バランサーは、**ユーザー サブスクリプション** アカウントには表示されますが、**Batch サービス** アカウントには表示されません。 Standard ロード バランサーでは、Batch プール VM との間でやり取りされるすべてのデータに対して料金が発生します。プール ノード (タスク/ノード ファイルの取得など)、タスク アプリケーション パッケージ、リソース/出力ファイル、およびコンテナー イメージからデータを取得する一部の Batch API には料金が発生します。

### <a name="additional-services"></a>その他のサービス

VM とストレージが含まれないサービスでは、Batch アカウントのコストを考慮に入れることができます。

Batch と共に一般的に使用される他のサービスには、次のものがあります。

- Application Insights
- Data Factory
- Azure Monitor
- Virtual Network
- グラフィック アプリケーションを使用する VM

お客様が Batch ソリューションと共に使用するサービスによっては、追加料金が発生する可能性があります。 各追加サービスのコストを確認するには、「[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)」を参照してください。

## <a name="cost-analysis-and-budget-for-a-pool"></a>プールのコスト分析と予算

Azure portal では、Batch プールまたは Batch アカウントの予算と支出アラートを作成できます。 予算とアラートは、過度の支出のリスクを関係者に通知するために役立ちます。 アラートの使用に遅れが生じ、予算をわずかに超える可能性があります。 この例では、個々の Batch プールのコスト分析を確認します。

1. Azure portal で、左側のナビゲーションバーから **[コストの管理と請求]** を選択します。
1. **[サブスクリプション]** セクションからサブスクリプションを選択します
1. 左のナビゲーション バーの **[コスト管理]** セクションにある **[コスト分析]** に移動します。次のようなビューが表示されます。
1. **[フィルターの追加]** を選択します 最初のドロップダウンで **[リソース]** を選択します。![リソース フィルターを選択する](./media/batch-budget/resource-filter.png)
1. 2 番目のドロップダウンで、Batch プールを選択します。 プールが選択されると、コスト分析は次の分析のようになります。
    ![プールのコスト分析](./media/batch-budget/pool-cost-analysis.png)

結果のコスト分析には、プールのコストと、そのコストの原因であるリソースが表示されます。 この例では、プールで使用されている VM が最もコストのかかるリソースです。

プールの予算を作成するには、 **[Budget: none]\(予算: なし\)** を選択してから **[新しい予算の作成 >]** を選択します。 ウィンドウを使用して、お使いのプールに合わせて予算を構成します。

予算の構成の詳細については、「[Azure の予算を作成して管理する](../cost-management/tutorial-acm-create-budgets.md)」を参照してください。

> [!NOTE]
> Azure Batch は Azure Cloud Services と Azure Virtual Machines テクノロジに基づいて構築されています。 **Cloud Services 構成**を選択した場合は、Cloud Services の料金体系に基づいて課金されます。 **仮想マシン構成**を選択した場合は、Virtual Machines の料金体系に基づいて課金されます。 このページの例では、**仮想マシン構成**を使用しています。

## <a name="minimize-cost"></a>コストを最小限に抑える

長期間にわたって複数の VM および Azure サービスを使用すると、コストが高くなる可能性があります。 幸いにも、支出を減らすために利用できるサービスと、ワークロードの効率を最大限に高めるための戦略があります。

### <a name="low-priority-virtual-machines"></a>優先順位の低い仮想マシン

優先順位の低い VM を使用すると、Azure の余剰コンピューティング容量が利用されるので、Batch ワークロードのコストが削減されます。 プールで優先順位の低い VM を指定すると、Batch ではこの余剰を使用してワークロードが実行されます。 専用 VM ではなく優先順位の低い VM を使用することで、コストを大幅に削減することができます。

ワークロードに優先順位の低い仮想マシンを設定する方法の詳細については、「[Batch で優先順位の低い VM を使用する](batch-low-pri-vms.md)」を参照してください。

### <a name="virtual-machine-os-disk-type"></a>仮想マシン OS ディスクの種類

[VM OS ディスクの種類](../virtual-machines/windows/disks-types.md)は複数あります。 ほとんどの VM シリーズには、Premium ストレージと Standard ストレージの両方をサポートするサイズがあります。 ’s’ VM サイズがプールに選択されている場合、Batch には PremiumSSD OS ディスクが構成されます。 ’s 以外’ の VM サイズを選択すると、より低コストな Standard HDD ディスクの種類が使用されます。 たとえば、Premium SSD OS ディスクは `Standard_D2s_v3` に使用され、Standard HDD OS ディスクは `Standard_D2_v3` に使用されます。

Premium SSD OS ディスクはより高価ですが、パフォーマンスが高く、Premium ディスクを使用する VM は Standard HDD OS ディスクを使用する VM よりもやや速く起動できます。 Batch では、OS ディスクはアプリケーションほどの頻度では使用されません。また、タスク ファイルは VM の一時 SSD ディスク上にあります。 そのため、多くの場合、’s’ VM サイズを指定した場合にプロビジョニングされる Premium SSD に追加コストを支払う必要はありません。

### <a name="reserved-virtual-machine-instances"></a>予約仮想マシン インスタンス

長期間 Batch を使用する予定の場合は、ワークロードに [Azure の予約](../billing/billing-save-compute-costs-reservations.md)を使用することで VM のコストを節約できます。 予約料金は、従量課金制の料金よりもかなり低価です。 予約なしで使用された仮想マシン インスタンスは、従量課金制で課金されます。 予約を購入すると、予約割引が適用され、従量課金制の料金では課金されなくなります。

### <a name="automatic-scaling"></a>自動スケーリング

[自動スケーリング](batch-automatic-scaling.md)では、現在のジョブの要求に基づいて、Batch プール内の VM 数が動的にスケーリングされます。 自動スケーリングでは、ジョブの有効期間に基づいてプールがスケールされるので、実行するジョブがある場合にのみ VM がスケールアップされ、使用されます。 ジョブが完了したとき、またはジョブがないときは、コンピューティング リソースを節約するために VM は自動的にスケールダウンされます。 スケーリングを使用すると、必要なリソースのみを使用して Batch ソリューションの全体的なコストを削減できます。

自動スケーリングの詳細については、「[Azure Batch プール内のコンピューティング ノードの自動スケール](batch-automatic-scaling.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- Batch ソリューションの構築と監視に使用できる [Batch API とツール](batch-apis-tools.md)の詳細を確認します。  

- [優先順位の低い VM と Batch](batch-low-pri-vms.md) の詳細を確認します。
