---
title: 予約容量を利用して Azure App Service のコストを節約する
description: Azure App Service Premium v3 の予約インスタンスおよび Isolated スタンプ料金のコストを節約する方法について説明します。
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 02/01/2021
ms.author: banders
ms.custom: references_regions
ms.openlocfilehash: 92a315121ad8ae6fadcadbf6d531eb3e99ae69a9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374543"
---
# <a name="save-costs-with-azure-app-service-reserved-instances"></a>Azure App Service 予約インスタンスを使用してコストを節約する

この記事では、Azure App Service 予約インスタンスで、Premium v3 インスタンスおよび Isolated スタンプ料金を節約する方法について説明します。

## <a name="save-with-premium-v3-reserved-instances"></a>Premium v3 予約インスタンスで節約する

Azure App Service Premium v3 予約インスタンスにコミットすると、コストを削減できます。 予約割引は、予約スコープおよび属性に一致する実行中のインスタンス数に対して自動的に適用されます。 割引を取得するために、インスタンスに予約を割り当てる必要はありません。

## <a name="determine-the-right-reserved-instance-size-before-you-buy"></a>購入する前に適切な予約インスタンス サイズを決定する

予約を購入する前に、Premium v3 予約インスタンスの必要サイズを決定する必要があります。 次のセクションは、適切な Premium v3 予約インスタンス サイズを決定するのに役立ちます。

### <a name="use-reservation-recommendations"></a>予約のレコメンデーションを使用する

予約のレコメンデーションを使用して、購入する必要がある予約の決定に役立てることができます。

- Azure portal で Premium v3 の予約インスタンスを購入するときに、購入のレコメンデーションと推奨される数量が示されます。
- Azure Advisor によって、個々のサブスクリプションに対して購入のレコメンデーションが提供されます。
- API を使用して、共有スコープと単一サブスクリプション スコープの両方に対する購入のレコメンデーションを取得できます。 詳細については、「[Reserved instance purchase recommendation APIs for enterprise customers](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)」 (エンタープライズ顧客向けの予約インスタンスの購入に関するレコメンデーション API) を参照してください。
- Enterprise Agreement (EA) および Microsoft Customer Agreement (MCA) の顧客の場合、共有スコープと単一サブスクリプション スコープにおける購入のレコメンデーションは、[Azure Consumption Insights の Power BI コンテンツ パック](/power-bi/service-connect-to-azure-consumption-insights)で利用できます。

### <a name="analyze-your-usage-information"></a>利用状況の情報を分析する

購入する必要がある予約の決定に役立てるために、利用状況の情報を分析します。 利用状況データは、利用状況ファイルと API で入手できます。 それらをまとめて使用し、購入する予約を決定します。 購入する予約の数量を決定するには、日単位の使用率が高い Premium v3 インスタンスを確認します。

使用状況ファイルは、請求期間と毎日の使用状況別の料金を示しています。 使用状況ファイルのダウンロードについては、「[Azure の使用量と料金の表示とダウンロード](../understand/download-azure-daily-usage.md)」を参照してください。 その後、使用状況ファイルの情報を使用して、[購入する予約を判断する](determine-reservation-purchase.md)ことができます。


## <a name="buy-a-premium-v3-reserved-instance"></a>Premium v3 予約インスタンスを購入する

Premium v3 予約インスタンスは [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D) で購入できます。 予約の支払いは、[前払いまたは月払い](prepare-buy-reservation.md)で行います。 Premium v3 予約インスタンスの購入には、次の要件が適用されます。

- 少なくとも 1 つの EA サブスクリプションまたは従量課金制料金のサブスクリプションの所有者ロールである必要があります。
- EA サブスクリプションの場合、[EA ポータル](https://ea.azure.com/)で **[予約インスタンスを追加します]** を有効にする必要があります。 または、その設定が無効になっている場合は、ユーザーはサブスクリプションの EA 管理者である必要があります。
- クラウド ソリューション プロバイダー (CSP) プログラムの場合、管理エージェントまたはセールス エージェントのみが予約購入できます。

インスタンスを購入する場合、次のことが必要です。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **[すべてのサービス]**  >  **[予約]** を選択します。
3. **[追加]** を選択して新しい予約を購入してから、 **[インスタンス]** をクリックします。
4. 必須フィールドに入力します。 選択した属性に一致する Premium v3 予約インスタンスを実行することで、予約割引を受けることができます。 割引を受ける Premium v3 予約インスタンスの実際の数は、選択したスコープと数量によって変わります。

EA 契約を結んでいる場合、 **[Add more option]\(さらにオプションを追加\)** を使用してインスタンスをすばやく追加することができます。 このオプションは、他のサブスクリプションの種類では使用できません。

| **フィールド** | **説明** |
|---|---|
| サブスクリプション | 予約の支払いに使用するサブスクリプション。 サブスクリプションの支払方法に対して、予約のコストが課金されます。 サブスクリプションの種類は、マイクロソフト エンタープライズ契約 (プラン番号:MS-AZR-0017P または MS-AZR-0148P) または Microsoft Customer Agreement または従来課金制の個々のサブスクリプション (プラン番号:MS-AZR-0003P または MS-AZR-0023P)。 年額コミットメント残高から料金が差し引かれるか(使用可能な場合)、超過料金として課金されます。 従量課金制料金のサブスクリプションの場合、クレジット カードまたはサブスクリプションの請求書に記載されている支払方法に料金が課金されます。 |
| Scope | 1 つのサブスクリプションまたは複数のサブスクリプション (共有スコープ) を予約のスコープにすることができます。 以下を選択した場合: <ul><li>**単一のリソース グループのスコープ** - 選択されたリソース グループ内の一致するリソースにのみ、予約割引を適用します。 </li><li>**単一サブスクリプション** - 選択されたサブスクリプションの一致するリソースに予約割引を適用します。</li><li>**共有スコープ** - 課金コンテキスト内にある有効なサブスクリプションの一致するリソースに予約割引を適用します。 EA の顧客の場合、課金コンテキストは課金です。 従量課金制料金の個々のサブスクリプションの場合、課金スコープはアカウント管理者によって作成されるすべての有効なサブスクリプションです。</li></ul> |
| リージョン | 予約の対象となる Azure リージョン。 |
| Premium v3 予約インスタンス サイズ | Premium v3 予約インスタンスのサイズです。 |
| 期間 | 1 年間または 3 年間。 また、HBv2 Premium v3 予約インスタンスでのみ使用できる 5 年間の期間もあります。 |
| Quantity | 予約内で購入しているインスタンス数。 この数量は、課金の割引を受けることができる実行中の Premium v3 予約インスタンスの数です。 たとえば、米国東部で Standard\_D2 Premium v3 予約インスタンスを 10 個実行している場合、実行中のすべての Premium v3 予約インスタンスのメリットを最大限に利用するには、数量を 10 と指定します。 |

## <a name="save-with-isolated-stamp-fees"></a>Isolated スタンプ料金で節約する

3 年分のスタンプ使用の予約をコミットすることで、Azure App Service 分離スタンプ税にかかるコストを節約できます。 分離スタンプ料金の予約容量を購入するには、スタンプがデプロイされる Azure リージョンと、購入するスタンプ数を選択する必要があります。

予約を購入すると、予約の属性に一致する Isolated スタンプ料金の使用状況は従量課金制で課金されなくなります。 予約は、予約容量のスコープとリージョンに一致する Isolated スタンプ数に自動的に適用されます。 Isolated スタンプに予約を割り当てる必要はありません。 予約は worker には適用されないので、スタンプに関連する他のすべてのリソースには別途課金されます。

予約容量が期限切れになると、Isolated スタンプは引き続き実行されますが、従量課金制の料金が課金されます。 予約は自動更新されません。

## <a name="determine-the-right-isolated-stamp-reservation-to-purchase"></a>購入する適切な Isolated スタンプ予約を判断する

予約を購入すると、今後 3 年分の予約量の使用に対してコミットすることになります。 使用状況データを確認し、継続して使用している App Service Isolated スタンプ数と、今後使用する可能性のある数を判断します。

さらに、Isolated スタンプから Linux または Windows の測定がどのように生成されるかについては、必ず理解してください。

- 空の Isolated スタンプからは、既定で Windows スタンプの測定が生成されます。 たとえば、worker がデプロイされていない場合などです。 Windows worker がスタンプにデプロイされている場合は、この測定が引き続き生成されます。
- Linux worker をデプロイしている場合、測定は Linux スタンプの測定に変わります。
- Linux と Windows の両方の worker がデプロイされている場合、スタンプからは Windows の測定が生成されます。

そのため、スタンプの有効期間中、スタンプの測定は Windows と Linux とで変わる可能性があります。

スタンプ上に 1 つ以上の Windows worker がある場合は、Windows スタンプ予約を購入します。 Linux スタンプ予約を購入する必要があるのは、スタンプに Linux worker _のみ_ を使用する予定の場合だけです。

## <a name="buy-isolated-stamp-reserved-capacity"></a>Isolated スタンプの予約容量を購入する

Isolated スタンプの予約容量は [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D) で購入できます。 予約の支払いは、[前払いまたは月払い](./prepare-buy-reservation.md)で行います。 予約容量を購入するには、少なくとも 1 つのエンタープライズ サブスクリプションまたは従量課金制料金の個人サブスクリプションの所有者ロールが必要です。

- エンタープライズ サブスクリプションの場合、[EA ポータル](https://ea.azure.com/)で **[予約インスタンスを追加します]** を有効にする必要があります。 また、その設定が無効な場合は、EA 管理者である必要があります。
- クラウド ソリューション プロバイダー (CSP) プログラムの場合、管理者エージェントまたはセールス エージェントのみが Azure Synapse Analytics の予約容量を購入できます。

**購入方法:**

1. [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D) にアクセスします。
1. サブスクリプションを選択します。 **[サブスクリプション]** リストを使用して、予約容量の支払いに使用するサブスクリプションを選択します。 サブスクリプションの支払方法に対して、予約容量のコストが課金されます。 サブスクリプションの種類は、マイクロソフト エンタープライズ契約 (プラン番号:MS-AZR-0017P または MS-AZR-0148P) または従量課金制 (プラン番号:MS-AZR-0003P または MS-AZR-0023P) または CSP サブスクリプション。
    - エンタープライズ サブスクリプションの場合、登録の Azure 前払い (旧称: 年額コミットメント) 残高から料金が差し引かれるか、超過分として課金されます。
    - 従量課金制サブスクリプションの場合、クレジット カードまたはサブスクリプションの請求書に記載されている支払方法に料金が課金されます。
1. **[スコープ]** を選択してサブスクリプションのスコープを選択します。
    - **単一のリソース グループのスコープ** - 選択されたリソース グループ内の一致するリソースにのみ、予約割引を適用します。
    - **単一サブスクリプション** - 選択されたサブスクリプションの一致するリソースに予約割引を適用します。
    - **共有スコープ** - 課金コンテキスト内にある有効なサブスクリプションの一致するリソースに予約割引を適用します。 マイクロソフト エンタープライズ契約のお客様の場合、課金コンテキストは登録です。 従量課金制料金の個々のサブスクリプションの場合、課金スコープはアカウント管理者によって作成されるすべての有効なサブスクリプションです。
1. **[リージョン]** を選択して、予約容量の対象となる Azure リージョンを選択し、予約をカートに追加します。
1. Isolated プランの種類を選択し、 **[選択]** をクリックします。  
    ![例](./media/prepay-app-service/app-service-isolated-stamp-select.png)
1. 予約する App Service Isolated スタンプ数を入力します。 たとえば、数量が 3 の場合、1 つのリージョンに 3 つの予約スタンプが付与されます。 **[次へ: Review + Buy]\(レビューと購入\)** をクリックします。
1. 確認して **[今すぐ購入]** をクリックします。

購入後は、いつでも [[予約]](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) に移動して購入の状態を確認および監視できます。

## <a name="cancel-exchange-or-refund-reservations"></a>予約の取り消し、交換、または返金

一定の制限付きで、予約の取り消し、交換、または返金を行うことができます。 詳しくは、「[Azure の予約のセルフサービスによる交換と払戻](exchange-and-refund-azure-reservations.md)」を参照してください。

## <a name="discount-application-shown-in-usage-data"></a>使用状況データに表示される割引の適用

使用状況データには、予約割引が適用される使用量に対する 0 の実効価格があります。 使用状況データは、各予約内の各スタンプ インスタンスの予約割引を示します。

マイクロソフト エンタープライズ契約 (EA) のお客様の場合、使用状況データに予約割引を表示する方法の詳細については、「[Enterprise Agreement の予約のコストと使用状況を取得する](understand-reserved-instance-usage-ea.md)」を参照してください。 それ以外の場合は、「[従量課金制料金のサブスクリプションの個別サブスクリプションの Azure 予約の使用状況について](understand-reserved-instance-usage.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- Azure の予約の詳細については、次の記事を参照してください。
  - [Azure の予約とは](save-compute-costs-reservations.md)
  - [Azure App Service Isolated スタンプの予約割引の適用方法の概要](reservation-discount-app-service.md)
  - [エンタープライズ加入契約の予約使用量について](understand-reserved-instance-usage-ea.md)
