---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 07/11/2019
ms.openlocfilehash: 766856438b22661b961bfbadc0b63376031622f6
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850793"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances-ri"></a>Azure Reserved VM Instances (RI) による仮想マシンの前払い

Azure Reserved Virtual Machine (VM) Instances を使って、仮想マシンの料金を前払いして、コストを節約しましょう。 詳しくは、[Azure Reserved VM Instances のオファー](https://azure.microsoft.com/pricing/reserved-vm-instances/)に関するページをご覧ください。

予約 VM インスタンスは [Azure Portal](https://portal.azure.com) から購入できます。 インスタンスを購入する場合、次のことが必要です。

- 少なくとも 1 つのエンタープライズ サブスクリプションまたは従量課金制料金のサブスクリプションの所有者ロールである必要があります。
- Enterprise サブスクリプションの場合、[EA ポータル](https://ea.azure.com)で **[予約インスタンスを追加します]** を有効にする必要があります。 または、その設定が無効になっている場合は、ユーザーはサブスクリプションの EA 管理者である必要があります。
- クラウド ソリューション プロバイダー (CSP) プログラムの場合、管理エージェントまたはセールス エージェントのみが予約購入できます。

予約割引は、予約スコープと属性に一致する実行中の仮想マシン数に対して自動的に適用されます。 予約のスコープは、[Azure Portal](https://portal.azure.com)、PowerShell、CLI、または API で更新できます。

## <a name="determine-the-right-vm-size-before-you-buy"></a>購入する前に適切な VM サイズを決定する

予約を購入する前に、必要な VM サイズを決定する必要があります。 次のセクションは、適切な VM サイズを決定するのに役立ちます。

### <a name="use-reservation-recommendations"></a>予約のレコメンデーションを使用する

予約のレコメンデーションを使用して、購入する必要がある予約の決定に役立てることができます。

- Azure portal で VM の予約インスタンスを購入するときに、購入のレコメンデーションと推奨される数量が示されます。
- Azure Advisor によって、個々のサブスクリプションに対して購入のレコメンデーションが提供されます。  
- API を使用して、共有スコープと単一サブスクリプション スコープの両方に対する購入のレコメンデーションを取得できます。 詳細については、「[Reserved instance purchase recommendation APIs for enterprise customers](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)」 (エンタープライズ顧客向けの予約インスタンスの購入に関するレコメンデーション API) を参照してください。
- EA の顧客の場合、共有スコープと単一サブスクリプション スコープにおける購入のレコメンデーションは、[Azure Consumption Insights の Power BI コンテンツ パック](/power-bi/service-connect-to-azure-consumption-insights)で利用できます。

### <a name="services-that-get-vm-reservation-discounts"></a>VM の予約割引が適用されるサービス

VM の予約は、VM のデプロイだけでなく、複数のサービスから発生する VM の使用量にも適用できます。 予約割引が適用されるリソースは、インスタンス サイズの柔軟性の設定によって変わります。

#### <a name="instance-size-flexibility-setting"></a>インスタンス サイズの柔軟性の設定

インスタンス サイズの柔軟性の設定によって、予約インスタンスの割引が適用されるサービスが決まります。

設定がオンかオフかにかかわらず、*ConsumedService* が `Microsoft.Compute` の場合は、条件に一致する VM の使用量に予約割引が自動適用されます。 そのため、*ConsumedService* の値に対応した使用量データを確認してください。 次に例をいくつか示します。

- 仮想マシン
- 仮想マシン スケール セット
- コンテナー サービス
- Azure Batch のデプロイ (ユーザー サブスクリプション モード)
- Azure Kubernetes Service (AKS)
- Service Fabric

設定がオンの場合、*ConsumedService* が次の項目のいずれかであれば、条件に一致する VM の使用量に予約割引が自動適用されます。

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

使用量データで *ConsumedService* の値を確認して、その使用量が予約割引の対象であるかどうか判断してください。

インスタンス サイズの柔軟性の詳細については、「[Reserved VM Instances での仮想マシン サイズの柔軟性](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md)」を参照してください。

### <a name="analyze-your-usage-information"></a>利用状況の情報を分析する
購入する必要がある予約の決定に役立てるために、利用状況の情報を分析します。

利用状況データは、利用状況ファイルと API で入手できます。 それらをまとめて使用し、購入する予約を決定します。 購入する予約の数量を決定するには、日常的に使用率が高い VM インスタンスを確認します。

利用状況データの `Meter` サブカテゴリと `Product` フィールドは避けます。 これらは、Premium Storage を使用する VM サイズは区別されません。 これらのフィールドを使用して予約の購入に対する VM サイズを決定した場合、不適切なサイズを購入する可能性があります。 そのため、期待した予約割引を受けることができなくなります。 代わりに、利用状況ファイルの `AdditionalInfo` フィールドまたは利用状況 API を参照して、適切な VM サイズを決定します。

### <a name="purchase-restriction-considerations"></a>購入の制限に関する考慮事項

予約 VM インスタンスはほとんどの VM サイズで利用できますが、一部例外があります。 次の VM には予約割引は適用されません。

- **VM シリーズ**: A シリーズ、Av2 シリーズ、または G シリーズ

- **プレビュー段階にある VM**: プレビュー段階にあるすべての VM シリーズまたはサイズ

- **クラウド**: ドイツまたは中国の各リージョンでは、予約購入を利用できません。

- **クォータの不足**: 単一サブスクリプションをスコープとする予約の場合、新しい予約インスタンスに利用できる vCPU クォータがそのサブスクリプションに存在していることが必要です。 たとえば対象のサブスクリプションに、D-Series に対して 10 vCPU のクォータ制限がある場合、Standard_D1 インスタンス 11 個分の予約を購入することはできません。 予約のクォータ チェックには、既にサブスクリプションにデプロイされている VM が含まれます。 たとえば、サブスクリプションに D-Series に対する 10 vCPU のクォータがあり、2 つの standard_D1 インスタンスがデプロイされている場合、このサブスクリプションでは、10 standard_D1 インスタンスの予約を購入することができます。 [クォータを増やす要求を作成](../articles/azure-supportability/resource-manager-core-quotas-request.md)して、この問題を解決できます。

- **容量制限**: まれなケースですが、VM サイズのサブセットに関して、リージョンのキャパシティが低下しているために、新しい予約を購入できないよう Azure によって制限されます。

## <a name="buy-a-reserved-vm-instance"></a>予約 VM インスタンスの購入

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. **[すべてのサービス]**  >  **[予約]** を選択します。
1. **[追加]** を選択して新しい予約を購入してから、 **[仮想マシン]** をクリックします。
1. 必須フィールドに入力します。 選択した属性と一致する VM インスタンスを実行することで、予約割引を受けることができます。 割引を受ける VM インスタンスの実際の数は、選択したスコープと数量によって変わります。

| フィールド      | 説明|
|------------|--------------|
|Subscription|予約の支払いに使用するサブスクリプション。 サブスクリプションの支払方法に対して、予約の初期コストが課金されます。 サブスクリプションの種類は、マイクロソフト エンタープライズ契約 (プラン番号:MS-AZR-0017P または MS-AZR-0148P) または従来課金制の個々のサブスクリプション (プラン番号: MS-AZR-0003P または MS-AZR-0023P)。 エンタープライズ サブスクリプションの場合、登録の年額コミットメント残高から料金が差し引かれるか、超過料金として課金されます。 従量課金制料金のサブスクリプションの場合、クレジット カードまたはサブスクリプションの請求書に記載されている支払方法に料金が課金されます。|    
|Scope (スコープ)       |1 つのサブスクリプションまたは複数のサブスクリプション (共有スコープ) を予約のスコープにすることができます。 以下を選択した場合: <ul><li>**単一のリソース グループのスコープ** — 選択されたリソース グループ内の一致するリソースにのみ、予約割引を適用します。</li><li>**単一サブスクリプションのスコープ** — 選択されたサブスクリプションの一致するリソースに予約割引を適用します。</li><li>**共有スコープ** — 課金コンテキスト内にある有効なサブスクリプションの一致するリソースに予約割引を適用します。 マイクロソフト エンタープライズ契約のお客様の場合、課金コンテキストは登録です。 従量課金制料金の個々のサブスクリプションの場合、課金スコープはアカウント管理者によって作成されるすべての有効なサブスクリプションです。</li></ul>|
|リージョン    |予約の対象となる Azure リージョン。|    
|VM サイズ     |VM インスタンスのサイズ|
|最適化の対象     |VM インスタンス サイズの柔軟性は既定で選択されています。 インスタンス サイズの柔軟性の値を変更して、同じ [VM サイズグループ](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md)の他の VM に予約割引を適用するには、 **[詳細設定]** をクリックします。 容量の優先度では、デプロイ用のデータ センターの容量が優先されます。 それにより、必要なときに VM インスタンスを起動する能力に対する信頼が高まります。 容量の優先順位は、予約のスコープが単一サブスクリプションに設定されている場合にのみ使用できます。 |
|期間        |1 年間または 3 年間。|
|Quantity    |予約内で購入しているインスタンス数。 数量は、課金の割引を受けられる実行中の VM インスタンス数です。 たとえば、米国東部で Standard_D2 VM を 10 個実行している場合、実行中のすべての VM のメリットを最大限に利用するには、数量を 10 と指定します。 |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="change-a-reservation-after-purchase"></a>購入後に予約を変更する

購入後に予約に次の種類の変更を加えることができます。

- 予約スコープの更新
- インスタンス サイズの柔軟性 (該当する場合)
- 所有権

また、予約をより小さいチャンクに分割したり、既に分割あれている予約を統合したりすることもできます。 いかなる変更であっても、新しい商用のトランザクションを発生させたり、予約の終了日が変更されたりすることはありません。

購入後に直接、次の種類の変更を加えることはできません。

- 既存の予約のリージョン
- SKU
- Quantity
- Duration

ただし、変更を加える必要がある場合、予約を*交換*することができます。

## <a name="cancellations-and-exchanges"></a>キャンセルと交換

ご自身の予約をキャンセルする必要がある場合は、12% の中途解約料が発生する可能性があります。 払い戻し額は、購入価格または予約の現在の価格のいずれかの最低価格に基づいています。 払い戻し額の上限は年額 50,000 ドルです。 日割り計算によって算出された金額から 12% の中途解約料を差し引いた金額が払い戻されます。 キャンセルを要求するには、Azure portal で予約に移動し、 **[返金]** を選択して、サポート要求を作成します。

ご自身の予約 VM インスタンスの予約を別のリージョン、VM サイズ グループ、または期間に変更する必要がある場合は、その予約を交換できます。 同等以上の価値がある別の予約に交換する必要があります。 新しい予約期間の開始日は、元の予約からは引き継がれません。 1 年または 3 年の予約期間は、新しい予約を作成した時点から始まります。 交換を要求するには、Azure portal で予約に移動し、 **[交換]** を選択して、サポート要求を作成します。

予約を交換または返金する方法の詳細については、[予約の交換と返金](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md)に関するページを参照してください。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。

## <a name="next-steps"></a>次の手順

- 予約を管理する方法については、「[Azure の予約の管理](../articles/billing/billing-manage-reserved-vm-instance.md)」をご覧ください。
- Azure の予約の詳細については、次の記事を参照してください。
    - [Azure の予約とは](../articles/billing/billing-save-compute-costs-reservations.md)
    - [Azure での予約の管理](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [予約割引の適用方法について](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [従量課金制料金のサブスクリプションの予約の使用状況について](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [エンタープライズ加入契約の予約使用量について](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [予約に含まれない Windows ソフトウェアのコスト](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [パートナー センターのクラウド ソリューション プロバイダー (CSP) プログラムでの Azure の予約](https://docs.microsoft.com/partner-center/azure-reservations)
