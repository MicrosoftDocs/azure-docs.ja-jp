---
title: Azure Cosmos DB の予約容量でコストを最適化する
description: Azure Cosmos DB の予約容量を購入して計算コストを節約する方法について説明します。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 5c2d1d286572b21879742a1a9c6ab3975441373d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98602684"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Azure Cosmos DB の予約容量でコストを最適化する
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB の予約容量を利用すると、Azure Cosmos DB リソースの 1 年間または 3 年間の予約をコミットすることによって、経費を節減できます。 Azure Cosmos DB の予約容量では、Cosmos DB リソース用にプロビジョニングされたスループットで割引を受けることができます。 リソースとはたとえば、データベースやコンテナー (テーブル、コレクション、およびグラフ) です。

Azure Cosmos DB 予約容量では、Cosmos DB にかかる費用を大幅に削減できます。1 年分または 3 年分の前払いにより、最大で 65 % の割引が可能になります。 予約容量では、割引が適用されても、Azure Cosmos DB リソースのランタイム状態は維持されます。

Azure Cosmos DB の予約容量では、リソース用にプロビジョニングされたスループットが対象になります。 ストレージとネットワーク料金は対象外です。 予約を購入するとすぐに、予約の属性に一致するスループット料金は従量課金制で課金されなくなります。 予約について詳しくは、「[Azure の予約とは](../cost-management-billing/reservations/save-compute-costs-reservations.md)」をご覧ください。

Azure Cosmos DB の予約容量は、[Azure portal](https://portal.azure.com) から購入できます。 予約の支払いは、[前払いまたは月払い](../cost-management-billing/reservations/prepare-buy-reservation.md)で行います。 予約容量を購入するには:

* 少なくとも 1 つのエンタープライズ サブスクリプションまたは従量課金制料金の個々のサブスクリプションで所有者ロールである必要があります。  
* Enterprise サブスクリプションの場合、[EA ポータル](https://ea.azure.com)で **[予約インスタンスを追加します]** を有効にする必要があります。 または、その設定が無効になっている場合は、ユーザーはサブスクリプションの EA 管理者である必要があります。
* クラウド ソリューション プロバイダー (CSP) プログラムの場合、管理者エージェントまたはセールス エージェントのみが Azure Cosmos DB の予約容量を購入できます。

## <a name="determine-the-required-throughput-before-purchase"></a>購入する前に必要なスループットを決定する

予約容量の購入のサイズは、既存の、または間もなくデプロイされる Azure Cosmos DB リソースが時間単位で使用するスループットの合計量を基にする必要があります。 次に例を示します。30,000 RU/秒の予約容量が時間単位の一貫した使用パターンである場合は、それを購入します。 この例で、30,000 RU/秒を超えてプロビジョニングされたスループットは、従量課金制の料金を使って課金されます。 プロビジョニングされたスループットが 1 時間あたり 30,000 RU/秒未満の場合、その時間の余剰の予約容量は無駄になります。

購入の推奨は、お客様の時間単位の使用パターンに基づいて計算されます。 過去 7 日間、30 日間、および 60 日間の使用量が分析され、お客様の節約額が最大になる予約容量の購入が推奨されます。 推奨される予約サイズは、次の手順を使用して Azure portal で確認できます。

1. [Azure portal](https://portal.azure.com) にサインインします。  

2. **[すべてのサービス]**  >  **[予約]**  >  **[追加]** を選択します。

3. **[購入予約]** ペインで **[Azure Cosmos DB]** を選択します。

4. 推奨される予約を表示するには、 **[推奨]** タブを選択します。

次の属性を使用して、推奨をフィルター処理できます。

- **期間** (1 年または 3 年)
- **請求頻度** (毎月または前払い)
- **スループットの種類** (RU/秒とマルチリージョン書き込み RU/秒)

また、推奨のスコープを 1 つのリソース グループ、1 つのサブスクリプション、または Azure の登録全体に限定することができます。 

推奨の例を次に示します。

:::image type="content" source="./media/cosmos-db-reserved-capacity/reserved-capacity-recommendation.png" alt-text="予約容量に関する推奨事項":::

30,000 RU/秒の予約を購入するこの推奨は、3 年の予約の中で、30,000 RU/秒の予約サイズにすると、節約が最大になることを示しています。 この場合、推奨は Azure Cosmos DB の過去 30 日間の使用量に基づいて計算されます。 このお客様は、Azure Cosmos DB の過去 30 日間の使用量が将来の使用量を表していると想定する場合、30,000 RU/秒の予約を購入すると、節約を最大になります。

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Azure Cosmos DB の予約容量を購入する

1. [Azure portal](https://portal.azure.com) にサインインします。  

2. **[すべてのサービス]**  >  **[予約]**  >  **[追加]** を選択します。  

3. **[購入予約]** ウィンドウから **[Azure Cosmos DB]** を選択して、新しい予約を購入します。  

4. 次の表で説明するように、必須フィールドに入力します。

   :::image type="content" source="./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png" alt-text="予約容量フォームに入力する":::

   |フィールド  |説明  |
   |---------|---------|
   |Scope   |   予約に関連づけられた課金の特典を使用できるサブスクリプションの数を制御するオプションです。 また、特定のサブスクリプションに予約を適用する方法も制御します。 <br/><br/>  **[共有]** を選択すると、予約割引は、課金のコンテキスト内にある任意のサブスクリプションで実行されている Azure Cosmos DB インスタンスに適用されます。 課金のコンテキストは、Azure に対するサインアップ方法に基づきます。 エンタープライズのお客様の場合、共有スコープが対象の登録であり、登録内のすべてのサブスクリプションが含まれます。 従量課金制のお客様の場合、共有スコープはアカウント管理者が作成するすべての個別の従量課金制サブスクリプションです。  <br/><br/>  **[単一サブスクリプション]** を選択すると、予約割引は選択したサブスクリプションの Azure Cosmos DB インスタンスに適用されます。 <br/><br/> **[1 つのリソース グループ]** を選択した場合、予約割引は、選択したサブスクリプションおよびそのサブスクリプション内の選択したリソース グループ内の Azure Cosmos DB インスタンスに適用されます。 <br/><br/> 予約容量を購入した後で、予約のスコープを変更できます。  |
   |サブスクリプション  |   Azure Cosmos DB の予約容量の支払いに使用するサブスクリプションです。 選択したサブスクリプションの支払方法が、コストの課金で使用されます。 サブスクリプションは、次のいずれかの種類である必要があります。 <br/><br/>  マイクロソフト エンタープライズ契約 (オファー番号:MS-AZR-0017P または MS-AZR-0148P):Enterprise サブスクリプションの場合、登録の Azure 前払い (旧称: 年額コミットメント) の残高から料金が差し引かれるか、超過分として課金されます。 <br/><br/> 従量課金制料金の個別サブスクリプション (オファー番号:MS-AZR-0003P または MS-AZR-0023P):従量課金制料金の個々のサブスクリプションの場合、クレジット カードまたはサブスクリプションの請求書に記載されている支払方法に料金が課金されます。    |
   | リソース グループ | 予約容量割引が適用されるリソース グループ。 |
   |期間  |   1 年間または 3 年間。   |
   |スループットの種類   |  スループットは、要求ユニットとしてプロビジョニングされます。 両方の設定 (1 つのリージョンの書き込みと複数のリージョンの書き込み) のプロビジョニング済みスループットの予約を購入できます。 スループットの種類は、2 つの値から選択できます。1 時間あたり 100 RU/秒と 1 時間あたり 100 マルチリージョン書き込み RU/秒です。|
   | 予約容量ユニット| 予約するスループットの量です。 リージョンごとのすべての Cosmos DB リソース (データベースやコンテナーなど) に必要なスループットを決定することで、この値を計算できます。 次に、Cosmos データベースに関連付けるリージョンの数を掛け合わせます。 次に例を示します。5 つのリージョンがあり、すべてのリージョンが 100万 RU/秒である場合は、予約容量の購入に 500万 RU/秒を選択します。 |


5. フォームに入力すると、予約容量の購入に必要な価格が計算されます。 出力には、選択したオプションで受けられる割引率も表示されます。 次に **[選択]** をクリックします

6. **[購入予約]** ウィンドウで、予約の割引と価格を確認します。 この予約価格は、すべてのリージョンでスループットがプロビジョニングされている Azure Cosmos DB リソースに適用されます。  

   :::image type="content" source="./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png" alt-text="予約容量の概要":::

7. **[Review + buy]\(レビュー + 購入\)** 、 **[今すぐ購入]** の順に選択します。 購入が成功すると、次のようなページが表示されます。

予約を購入すると、予約の条件に一致する既存の Azure Cosmos DB リソースにすぐに適用されます。 既存の Azure Cosmos DB リソースを持っていない場合は、予約の条件に一致する新しい Cosmos DB インスタンスをデプロイすると、予約が適用されます。 どちらの場合にも、予約の期間は正常な購入の直後に開始されます。

予約の期限が切れると、Azure Cosmos DB インスタンスは引き続き実行し、正規の従量課金制の料金で課金されます。

## <a name="cancel-exchange-or-refund-reservations"></a>予約の取り消し、交換、または返金

一定の制限付きで、予約の取り消し、交換、または返金を行うことができます。 詳しくは、「[Azure の予約のセルフサービスによる交換と払戻](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

予約割引は、予約スコープと属性に一致する Azure Cosmos DB リソースに対して自動的に適用されます。 予約のスコープは、Azure portal、PowerShell、Azure CLI、または API で更新できます。

*  Azure Cosmos DB に予約容量割引が適用される方法については、「[Azure の予約割引の概要](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md)」をご覧ください。

* Azure の予約の詳細については、次の記事を参照してください。

   * [Azure の予約とは](../cost-management-billing/reservations/save-compute-costs-reservations.md)  
   * [Azure の予約の管理](../cost-management-billing/reservations/manage-reserved-vm-instance.md)  
   * [エンタープライズ加入契約の予約使用量について](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)  
   * [従量課金制サブスクリプションの予約使用量について](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
   * [Azure reservations in the Partner Center CSP program](/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。