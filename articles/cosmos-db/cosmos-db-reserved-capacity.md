---
title: Azure Cosmos DB リソースのコストを予約容量で最適化する
description: Azure Cosmos DB の予約容量を購入して計算コストを節約する方法について説明します。
author: bandersmsft
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: b74fcc2e08f02be7adeeab4cfee5f36d5194392c
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508640"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Azure Cosmos DB の予約容量でコストを最適化する

Azure Cosmos DB の予約容量は、リソースの料金 1 年分または 3 年分を前払いすることで経費を節減できるサービスです。 Azure Cosmos DB の予約容量では、Cosmos DB リソース用にプロビジョニングされたスループットで割引を受けることができます。 リソースとはたとえば、データベースやコンテナー (テーブル、コレクション、およびグラフ) です。

Azure Cosmos DB 予約容量では、Cosmos DB にかかる費用を大幅に削減できます。1 年分または 3 年分の前払いにより、最大で 65 % の割引が可能になります。 予約容量では、割引が適用されても、Azure Cosmos DB リソースのランタイム状態は維持されます。

Azure Cosmos DB の予約容量では、リソース用にプロビジョニングされたスループットが対象になります。 ストレージとネットワーク料金は対象外です。 予約を購入するとすぐに、予約の属性に一致するスループット料金は従量課金制で課金されなくなります。 予約について詳しくは、「[Azure の予約とは](../billing/billing-save-compute-costs-reservations.md)」をご覧ください。

Azure Cosmos DB の予約容量は、[Azure portal](https://portal.azure.com) から購入できます。 予約容量を購入するには:

* 少なくとも 1 つのエンタープライズ サブスクリプションまたは従量課金制料金の個々のサブスクリプションで所有者ロールである必要があります。  
* Enterprise サブスクリプションの場合、[EA ポータル](https://ea.azure.com)で **[予約インスタンスを追加します]** を有効にする必要があります。 または、その設定が無効になっている場合は、ユーザーはサブスクリプションの EA 管理者である必要があります。
* クラウド ソリューション プロバイダー (CSP) プログラムの場合、管理者エージェントまたはセールス エージェントのみが Azure Cosmos DB の予約容量を購入できます。

## <a name="determine-the-required-throughput-before-purchase"></a>購入する前に必要なスループットを決定する

予約のサイズは、既存の、または間もなくデプロイする Azure Cosmos DB リソースで使用されるスループットの合計量に基づく必要があります。 必要なスループットは次の方法で決定できます。

* すべてのリージョンの Azure Cosmos DB アカウント、データベース、コレクション全体でプロビジョニングされている合計スループットの履歴データを取得します。 たとえば、`https://account.azure.com` から毎日の使用量明細をダウンロードすることにより、毎日の平均のプロビジョニング済みスループットを評価できます。

* マイクロソフト エンタープライズ契約 (EA) のお客様であれば、使用状況ファイルをダウンロードして、Azure Cosmos DB のスループットの詳細を取得できます。 使用状況ファイルの **[追加情報]** セクションの **[サービスの種類]** 値を参照してください。

* 今後 1 年間または 3 年間実行することが予想される Azure Cosmos DB アカウントでの全ワークロードに対する平均スループットを合計できます。 その場合、その数量を予約に使用できます。

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Azure Cosmos DB の予約容量を購入する

1. [Azure Portal](https://portal.azure.com) にサインインします。  

2. **[すべてのサービス]**  >  **[予約]**  >  **[追加]** を選択します。  

3. **[購入予約]** ウィンドウから **[Azure Cosmos DB]** を選択して、新しい予約を購入します。  

4. 次の表で説明するように、必須フィールドに入力します。

   ![予約容量フォームに入力する](./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png)

   |フィールド  |説明  |
   |---------|---------|
   |Scope (スコープ)   |   予約に関連づけられた課金の特典を使用できるサブスクリプションの数を制御するオプションです。 また、特定のサブスクリプションに予約を適用する方法も制御します。 <br/><br/>  **[共有]** を選択すると、予約割引は、課金のコンテキスト内にある任意のサブスクリプションで実行されている Azure Cosmos DB インスタンスに適用されます。 課金のコンテキストは、Azure に対するサインアップ方法に基づきます。 エンタープライズのお客様の場合、共有スコープが対象の登録であり、登録内のすべてのサブスクリプションが含まれます。 従量課金制のお客様の場合、共有スコープはアカウント管理者が作成するすべての個別の従量課金制サブスクリプションです。  <br/><br/>  **[単一サブスクリプション]** を選択すると、予約割引は選択したサブスクリプションの Azure Cosmos DB インスタンスに適用されます。 <br/><br/> **[1 つのリソース グループ]** を選択した場合、予約割引は、選択したサブスクリプションおよびそのサブスクリプション内の選択したリソース グループ内の Azure Cosmos DB インスタンスに適用されます。 <br/><br/> 予約容量を購入した後で、予約のスコープを変更できます。  |
   |サブスクリプション  |   Azure Cosmos DB の予約容量の支払いに使用するサブスクリプションです。 選択したサブスクリプションの支払方法が、初期コストの課金で使用されます。 サブスクリプションの種類は、次のいずれかである必要があります。 <br/><br/>  マイクロソフト エンタープライズ契約 (オファー番号:MS-AZR-0017P または MS-AZR-0148P):エンタープライズ サブスクリプションの場合、登録の年額コミットメント残高から料金が差し引かれるか、超過料金として課金されます。 <br/><br/> 従量課金制料金の個別サブスクリプション (オファー番号:MS-AZR-0003P または MS-AZR-0023P):従量課金制料金の個々のサブスクリプションの場合、クレジット カードまたはサブスクリプションの請求書に記載されている支払方法に料金が課金されます。    |
   | リソース グループ | 予約容量割引が適用されるリソース グループ。 |
   |期間  |   1 年間または 3 年間。   |
   |スループットの種類   |  スループットは、要求ユニットとしてプロビジョニングされます。 両方の設定 (1 つのリージョンの書き込みと複数のリージョンの書き込み) のプロビジョニング済みスループットの予約を購入できます。 スループットの種類は、2 つの値から選択できます。1 時間あたり 100 RU/秒と 1 時間あたり 100 マルチマスター RU/秒です。|
   | 予約容量ユニット| 予約するスループットの量です。 リージョンごとのすべての Cosmos DB リソース (データベースやコンテナーなど) に必要なスループットを決定することで、この値を計算できます。 次に、Cosmos DB データベースに関連付けるリージョンの数を掛け合わせます。 例: 5 つのリージョンがあり、すべてのリージョンが 100万 RU/秒である場合は、予約容量の購入に 500万 RU/秒を選択します。 |
 

5. フォームに入力すると、予約容量の購入に必要な価格が計算されます。 出力には、選択したオプションで受けられる割引率も表示されます。 次に **[選択]** をクリックします

6. **[購入予約]** ウィンドウで、予約の割引と価格を確認します。 この予約価格は、すべてのリージョンでスループットがプロビジョニングされている Azure Cosmos DB リソースに適用されます。  

   ![予約容量の概要](./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png)

7. **[Review + buy]\(レビュー + 購入\)** 、 **[今すぐ購入]** の順に選択します。 購入が成功すると、次のようなページが表示されます。

予約を購入すると、予約の条件に一致する既存の Azure Cosmos DB リソースにすぐに適用されます。 既存の Azure Cosmos DB リソースを持っていない場合は、予約の条件に一致する新しい Cosmos DB インスタンスをデプロイすると、予約が適用されます。 どちらの場合にも、予約の期間は正常な購入の直後に開始されます。

予約の期限が切れると、Azure Cosmos DB インスタンスは引き続き実行し、正規の従量課金制の料金で課金されます。

## <a name="cancellation-and-exchanges"></a>キャンセルと交換

適正な予約容量を識別するには、「[Azure Cosmos DB に予約割引が適用されるしくみについて](../billing/billing-understand-cosmosdb-reservation-charges.md)」を参照してください。 Azure Cosmos DB の予約をキャンセルまたは交換する必要がある場合は、[予約の交換と返金](../billing/billing-azure-reservations-self-service-exchange-and-refund.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

予約割引は、予約スコープと属性に一致する Azure Cosmos DB リソースに対して自動的に適用されます。 予約のスコープは、Azure portal、PowerShell、Azure CLI、または API で更新できます。

*  Azure Cosmos DB に予約容量割引が適用される方法については、「[Azure の予約割引の概要](../billing/billing-understand-cosmosdb-reservation-charges.md)」をご覧ください。

* Azure の予約の詳細については、次の記事を参照してください。

   * [Azure の予約とは](../billing/billing-save-compute-costs-reservations.md)  
   * [Azure の予約の管理](../billing/billing-manage-reserved-vm-instance.md)  
   * [エンタープライズ加入契約の予約使用量について](../billing/billing-understand-reserved-instance-usage-ea.md)  
   * [従量課金制サブスクリプションの予約使用量について](../billing/billing-understand-reserved-instance-usage.md)
   * [Azure reservations in the Partner Center CSP program](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。
