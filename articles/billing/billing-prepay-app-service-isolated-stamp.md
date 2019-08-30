---
title: 予約容量を含む Azure App Service Isolated スタンプ料金の前払い
description: 予約容量を含む Azure App Service Isolated スタンプ料金を前払いしてコストを削減する方法について説明します。
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: banders
ms.openlocfilehash: f122ec2474c09cdd6c9ada4ddc59b1adb44f619f
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779994"
---
# <a name="prepay-for-azure-app-service-isolated-stamp-fee-with-reserved-capacity"></a>予約容量を含む Azure App Service Isolated スタンプ料金の前払い

3 年分のスタンプ使用料を前払いすることで、Azure App Service Isolated スタンプ料金にかかるコストを削減でます。 Isolated スタンプ料金の予約容量を購入するには、スタンプがデプロイされる Azure リージョンと、購入するスタンプ数を選択する必要があります。

予約を購入すると、予約の属性に一致する Isolated スタンプ料金の使用状況は従量課金制で課金されなくなります。 予約は、予約容量のスコープとリージョンに一致する Isolated スタンプ数に自動的に適用されます。 Isolated スタンプに予約を割り当てる必要はありません。 予約は worker には適用されないので、スタンプに関連する他のすべてのリソースには別途課金されます。

予約容量が期限切れになると、Isolated スタンプは引き続き実行されますが、従量課金制の料金が課金されます。 予約は自動更新されません。

## <a name="determine-the-right-reservation-to-purchase"></a>購入する適切な予約を判断する

予約を購入すると、今後丸 3 年間に予約量を使用する料金を前払いすることになります。 使用状況データを確認し、継続して使用している App Service Isolated スタンプ数と、今後使用する可能性のある数を判断します。

さらに、Isolated スタンプから Linux または Windows の測定がどのように生成されるかについては、必ず理解してください。

- 空の Isolated スタンプからは、既定で Windows スタンプの測定が生成されます。 たとえば、worker がデプロイされていない場合などです。 Windows worker がスタンプにデプロイされている場合は、この測定が引き続き生成されます。
- Linux worker をデプロイしている場合、測定は Linux スタンプの測定に変わります。
- Linux と Windows の両方の worker がデプロイされている場合、スタンプからは Windows の測定が生成されます。

そのため、スタンプの有効期間中、スタンプの測定は Windows と Linux とで変わる可能性があります。

スタンプ上に 1 つ以上の Windows worker がある場合は、Windows スタンプ予約を購入します。 Linux スタンプ予約を購入する必要があるのは、スタンプに Linux worker _のみ_を使用する予定の場合だけです。

## <a name="buy-isolated-stamp-reserved-capacity"></a>Isolated スタンプの予約容量を購入する

Isolated スタンプの予約容量は [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D) で購入できます。 予約容量を購入するには、少なくとも 1 つのエンタープライズ サブスクリプションまたは従量課金制料金の個人サブスクリプションの所有者ロールが必要です。

- エンタープライズ サブスクリプションの場合、[EA ポータル](https://ea.azure.com/)で **[予約インスタンスを追加します]** を有効にする必要があります。 また、その設定が無効な場合は、EA 管理者である必要があります。
- クラウド ソリューション プロバイダー (CSP) プログラムの場合、管理者エージェントまたはセールス エージェントのみが SQL Data Warehouse の予約容量を購入できます。

**購入方法:**

1. [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D) にアクセスします。
1. サブスクリプションを選択します。 **[サブスクリプション]** リストを使用して、予約容量の支払いに使用するサブスクリプションを選択します。 サブスクリプションの支払方法に対して、予約容量の初期コストが課金されます。 サブスクリプションの種類は、マイクロソフト エンタープライズ契約 (プラン番号:MS-AZR-0017P または MS-AZR-0148P) または従量課金制 (プラン番号:MS-AZR-0003P または MS-AZR-0023P) または CSP サブスクリプション。
    - エンタープライズ サブスクリプションの場合、登録の年額コミットメント残高から料金が差し引かれるか、超過料金として課金されます。
    - 従量課金制サブスクリプションの場合、クレジット カードまたはサブスクリプションの請求書に記載されている支払方法に料金が課金されます。
1. **[スコープ]** を選択してサブスクリプションのスコープを選択します。
    - **単一のリソース グループのスコープ** - 選択されたリソース グループ内の一致するリソースにのみ、予約割引を適用します。
    - **単一サブスクリプション** - 選択されたサブスクリプションの一致するリソースに予約割引を適用します。
    - **共有スコープ** - 課金コンテキスト内にある有効なサブスクリプションの一致するリソースに予約割引を適用します。 マイクロソフト エンタープライズ契約のお客様の場合、課金コンテキストは登録です。 従量課金制料金の個々のサブスクリプションの場合、課金スコープはアカウント管理者によって作成されるすべての有効なサブスクリプションです。
1. **[リージョン]** を選択して、予約容量の対象となる Azure リージョンを選択し、予約をカートに追加します。
1. Isolated プランの種類を選択し、 **[選択]** をクリックします。  
    ![例](./media/billing-prepay-app-service-isolated-stamp/app-service-isolated-stamp-select.png)
1. 予約する App Service Isolated スタンプ数を入力します。 たとえば、数量が 3 の場合、1 つのリージョンに 3 つの予約スタンプが付与されます。 **[次へ: Review + Buy]\(レビューと購入\)** をクリックします。
1. 確認して **[今すぐ購入]** をクリックします。

購入後は、いつでも [[予約]](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) に移動して購入の状態を確認および監視できます。

## <a name="cancel-exchange-or-refund-reservations"></a>予約の取り消し、交換、または返金

一定の制限付きで、予約の取り消し、交換、または返金を行うことができます。 詳しくは、「[Azure の予約のセルフサービスによる交換と払戻](billing-azure-reservations-self-service-exchange-and-refund.md)」を参照してください。

## <a name="discount-application-shown-in-usage-data"></a>使用状況データに表示される割引の適用

使用状況データには、予約割引が適用される使用量に対する 0 の実効価格があります。 使用状況データは、各予約内の各スタンプ インスタンスの予約割引を示します。

マイクロソフト エンタープライズ契約 (EA) のお客様の場合、使用状況データに予約割引を表示する方法の詳細については、「[Enterprise Agreement の予約のコストと使用状況を取得する](billing-understand-reserved-instance-usage-ea.md)」を参照してください。 それ以外の場合は、「[従量課金制料金のサブスクリプションの個別サブスクリプションの Azure 予約の使用状況について](billing-understand-reserved-instance-usage.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- Azure の予約の詳細については、次の記事を参照してください。
  - [Azure の予約とは](billing-save-compute-costs-reservations.md)
  - [Azure App Service Isolated スタンプの予約割引の適用方法の概要](billing-reservation-discount-app-service-isolated-stamp.md)
  - [エンタープライズ加入契約の予約使用量について](billing-understand-reserved-instance-usage-ea.md)
