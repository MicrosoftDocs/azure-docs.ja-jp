---
title: Azure BLOB ストレージのコストを計画および管理する
description: Azure portal でコスト分析を使用して、Azure BLOB ストレージのコストを計画および管理する方法を学習します。
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: 75b464c140bfda6c3f3559d3bfdbe1e6bc2e7f24
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/04/2020
ms.locfileid: "87760738"
---
# <a name="plan-and-manage-costs-for-azure-blob-storage"></a>Azure BLOB ストレージのコストを計画および管理する

この記事は、Azure BLOB ストレージのコストを計画および管理するのに役立ちます。 まず、Azure 料金計算ツールを使用して、コストを見積もります。 ストレージ アカウントを作成したら、必要な分だけ支払うようにアカウントを最適化します。 コスト管理機能を使用して、予算を設定し、コストを監視します。 また、予測コストを確認し、支出の傾向を監視して、対処した方がよい領域を識別することもできます。

BLOB ストレージのコストは、Azure の請求にある月額料金の一部でしかないことにご注意ください。 この記事では、BLOB ストレージのコストを見積もり、管理する方法について説明しますが、サード パーティのサービスを含め、Azure サブスクリプションで使用されるすべての Azure サービスとリソースに対して課金されます。 BLOB ストレージのコスト管理に慣れたら、同様の方法を適用して、サブスクリプションで使用されているすべての Azure サービスのコストを管理できます。

## <a name="estimate-costs"></a>コストの見積もり

Azure Storage アカウントを作成し、データの転送を開始する前にコストを見積もるには、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/) を使用します。

1. [[Azure 料金計算ツール]](https://azure.microsoft.com/pricing/calculator/) ページで、 **[ストレージ アカウント]** タイルを選択します。

2. ページを下方向にスクロールして、見積りの **[ストレージ アカウント]** セクションを見つけます。

3. ドロップダウン リストからオプションを選択します。 

   これらのドロップダウン リストの値を変更すると、コストの見積り額が変化します。 その見積り額は、上部の隅と見積りの下部に表示されます。 
    
   ![[コスト分析] ペインを使用してコストを監視する](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   **[種類]** ドロップダウン リストの値を変更すると、このワークシートに表示されている他のオプションも変更されます。 **[詳細]** セクションのリンクを使用して、各オプションの意味とこれらのオプションがストレージ関連の操作の価格にどのように影響するかを確認します。 

4. 残りのオプションを変更して、それらが見積もりに与える影響を確認します。

## <a name="optimize-costs"></a>コストを最適化する

コストを削減するには、これらのオプションを使用することを検討してください。 

- ストレージ容量を予約する

- データをアクセス層にまとめる

- アクセス層間でデータを自動的に移動する

このセクションでは、各オプションについて詳しく説明します。 

#### <a name="reserve-storage-capacity"></a>ストレージ容量を予約する

Azure Storage の予約容量を使用して BLOB データのストレージ コストを節約できます。 Azure Storage の予約容量により、1 年間または 3 年間の予約を行うと、Standard ストレージ アカウントのブロック BLOB および Azure Data Lake Storage Gen2 データの容量を割引できます。 予約を行うと、予約期間に一定量のストレージ容量が提供されます。 Azure Storage の予約容量により、ブロック BLOB と Azure Data Lake Storage Gen2 データの容量コストを大幅に削減できます。 

詳細については、「[予約容量を使用して BLOB ストレージのコストを最適化する](https://docs.microsoft.com/azure/storage/blobs/storage-blob-reserved-capacity)」を参照してください。

#### <a name="organize-data-into-access-tiers"></a>データをアクセス層にまとめる

コスト効率に優れたアクセス層に BLOB データを配置することで、コストを削減できます。 データの使用に関するコストを最適化するように設計された 3 つの層から選択します。 たとえば、"*ホット*" 層のストレージ コストは高くなりますが、アクセス コストは低くなります。 そのため、データに頻繁にアクセスする場合は、ホット層が最もコスト効率の高い選択肢になる可能性があります。 データにアクセスする頻度が少ない場合は、データを格納するコストを削減すると同時に、データにアクセスするコストが発生するため、"*コールド*" または "*アーカイブ*" 層が最も意味がある可能性があります。    

詳細については、「[Azure Blob Storage: ホット、クール、アーカイブ ストレージ層](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal)」を参照してください。

#### <a name="automatically-move-data-between-access-tiers"></a>アクセス層間でデータを自動的に移動する

ライフサイクル管理ポリシーを使用して、コストを最も節約するために定期的に階層間でデータを移動します。 これらのポリシーによって、指定したルールを使用してデータをに移動できます。 たとえば、BLOB が 90 日以内に変更されていない場合、BLOB をアーカイブ層に移動するルールを作成することができます。 データのアクセス層を調整するポリシーを作成することによって、ニーズに最もコストのかからないストレージ オプションを設計できます。

詳細については、「[Azure Blob Storage のライフサイクルを管理する](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)」を参照してください。

## <a name="create-budgets"></a>予算を作成する

[予算](../../cost-management-billing/costs/tutorial-acm-create-budgets.md)を作成してコストを管理し、異常な支出や超過出費のリスクについて関係者に自動的に通知するアラートを作成できます。 アラートは、予算とコストのしきい値と比較した支出に基づきます。 予算とアラートは、Azure サブスクリプションとリソース グループに対して作成されるため、全体的なコスト監視戦略の一環として役立ちます。 ただし、これらはより高いレベルでコストを追跡するように設計されているため、Azure Storage のコストなど、個々の Azure サービスのコストを管理する機能が限定されていることがあります。

## <a name="monitor-costs"></a>コストを監視する

Azure Storage で Azure リソースを使用する場合、コストが発生します。 リソース使用のユニット コストは、期間 (秒、分、時間、日数) やユニット使用量 (バイト、メガバイトなど) によって異なります。Azure Storage の使用を開始するとただちにコストが発生します。 コストは、Azure portal の [[コスト分析]](../../cost-management-billing/costs/quick-acm-cost-analysis.md) ペインで確認できます。

コスト分析を使用すると、さまざまな期間について、Azure Storage のコストをグラフや表で表示できます。 たとえば、日単位、現在の月、以前の月、年単位などがあります。 予算や予想コストを基準としてコストを表示することもできます。 長期にわたるより長い期間のビューに切り替えると、支出の傾向を特定し、超過出費が発生した可能性のある時期を確認するのに役立ちます。 予算を作成したら、それを超えた場合を簡単に確認できます。

>[!NOTE]
> コスト分析では、さまざまな種類の Azure アカウントがサポートされています。 サポートされているアカウントの種類の完全な一覧については、「[Understand Cost Management data (Cost Management データの概要)](../../cost-management-billing/costs/understand-cost-mgt-data.md)」を参照してください。 コスト データを表示するには、少なくとも Azure アカウントの読み取りアクセス許可が必要です。 Azure Cost Management データに対するアクセス権の割り当てについては、[データへのアクセス許可の割り当て](../../cost-management-billing/costs/assign-access-acm-data.md)に関するページを参照してください。

Azure Storage のコストをコスト分析で表示するには、次の操作を行います。

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. **[コストの管理と請求]** ウィンドウを開き、メニューから **[Cost management] (コスト管理)** を選択してから、 **[コスト分析]** を選択します。 その後、特定のサブスクリプションのスコープを **[スコープ]** ドロップダウンから変更できます。

   ![[コスト分析] ペインを使用してコストを監視する](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Azure Storage のコストのみを表示するには、 **[フィルターの追加]** 、 **[サービス名]** の順に選択します。 次に、リストから **[ストレージ]** を選択します。 

   Azure Storage のみのコストを表示する例を次に示します。

   ![[コスト分析] ペインを使用してストレージ コストを監視する](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

前の例では、サービスの現在のコストが表示されます。 また、Azure リージョン (場所) 別およびリソース グループ別のコストも表示されます。 他のフィルターを追加することもできます (例: 特定のストレージ アカウントのコストを表示するフィルター)。

## <a name="next-steps"></a>次の手順

[コスト分析](../../cost-management-billing/costs/quick-acm-cost-analysis.md)を使用してコストを管理する方法について詳細に説明します。

Azure Storage での課金のしくみの詳細については、以下の記事を参照してください。

- [Azure Storage の価格の概要](https://azure.microsoft.com/pricing/details/storage/)
- [予約容量を使用して BLOB ストレージのコストを最適化する](../blobs/storage-blob-reserved-capacity.md)
