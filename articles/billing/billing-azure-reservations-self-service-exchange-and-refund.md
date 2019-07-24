---
title: Azure の予約のセルフサービスによる交換と払戻 | Microsoft Docs
description: Azure の予約を交換または払い戻す方法について説明します。
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/5/2019
ms.author: banders
ms.openlocfilehash: aa1a218fbf0bc7eacac65b50e4ee1f86791e2b3b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281983"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Azure の予約のセルフサービスによる交換と払戻

Azure の予約は、変化するニーズを満たすために柔軟性を提供します。 変化するニーズを満たすために、予約を同じ種類の別の予約と交換できます。 また、予約が不要になった場合は、最大で年間 50,000 米国ドルまでの払い戻しができます。

セルフ サービスの交換およびキャンセルの機能は、米国政府のエンタープライズ契約のお客様は利用できません。 従量課金制や CSP などの他の種類の米国政府機関向けサブスクリプションがサポートされています。

## <a name="exchange-an-existing-reserved-instance"></a>既存の予約済みインスタンスを交換する

[Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) での簡単な 3 つの手順により予約を交換できます。

1. 払い戻したい予約を選択して **[交換]** をクリックします。  
    ![返金する予約を示す例の画像](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-return.png)
2. 購入する VM 製品を選択し、数量を入力します。 新しい購入合計が、返金合計より多くなるようにしてください。 [購入する前に適切なサイズを決定](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)します。  
    ![交換による VM 製品の購入を示す例の画像](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-select-purchase.png)
3. トランザクションを確認して、完了します。  
    ![交換による VM 製品の購入で返金を完了する例の画像](./media/billing-azure-reservations-self-service-exchange-and-refund/exchange-refund-confirm-exchange.png)

予約を払い戻すには、**[予約の詳細]** に移動し、**[払戻]** をクリックします。

## <a name="how-return-and-exchange-transactions-are-processed"></a>返金および交換トランザクションの処理方法

最初に、Microsoft が既存の予約を取り消し、その予約の日割り金額を払い戻します。 交換がある場合は、新しい購入が処理されます。 アカウントの種類と支払い方法に応じて、Microsoft は次のいずれかの方法を使用して払戻を処理します。

### <a name="enterprise-agreement-customers"></a>エンタープライズ契約のお客様

元の購入が年額コミットメントを使用して購入されていた場合は、年額コミットメントに交換の金額が加算されて払い戻されます。 元の購入以来の超過分の請求書は、年額コミットメントが使用されるように、再度開かれて再計算されます。 予約を購入するのに使用された年額コミットメントの期間がアクティブでなくなっている場合は、現在のエンタープライズ契約年額コミットメント期間にクレジットを追加できます。

元の購入が超過であった場合、Microsoft はクレジット メモを発行します。

### <a name="pay-as-you-go-invoice-payment-customers-and-cloud-solution-provider-program"></a>従量課金制の請求書支払のお客様とクラウド ソリューション プロバイダー プログラム

元の予約購入請求書がキャンセルされ、払戻用の新しい請求書が作成されます。 交換の場合、新しい請求書には払戻と新しい購入が示されます。 払戻額は購入に対して調整されます。 予約の払戻のみの場合は、日割り金額が保持され、今後の予約購入に対して調整されます。

### <a name="pay-as-you-go-credit-card-customers"></a>従量課金制クレジット カードのお客様

元の請求書は取り消され、新しい請求書が作成されます。 元の購入に使用されたクレジット カードに金額が払い戻されます。 カードを変更した場合は、[サポートにお問い合わせください](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="exchange-policies"></a>交換ポリシー

- 既存の複数の予約を返金して、同じ種類の新しい予約を購入できます。 別の種類の予約と交換することはできません。 たとえば、VM の予約を返金して、SQL の予約を購入することはできません。
- 交換を処理できるのは、予約の所有者のみです。 [予約を管理できるユーザーを追加または変更する](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance#add-or-change-users-who-can-manage-a-reservation)方法を参照してください。
- 交換は払戻と再購入として処理されます。キャンセルと新しい購入には、異なるトランザクションが作成されます。 日割り計算された予約金額が、交換する予約に払い戻されます。 新しい購入は全額請求されます。 日割り計算された予約金額は、返金される予約の日割り計算された残余値です。
- 予約を購入するために使用したエンタープライズ契約の有効期限が切れて、新しい契約として更新された場合も、予約を交換または払い戻すことができます。
- 交換により、サイズ、リージョン、数量、期間などの予約のプロパティを変更できます。
- 新しい購入合計は、返金額以上でなければなりません。
- 交換の一部として購入された新しい予約には、交換の時点から開始される新しい期間が適用されます。
- 交換に対するペナルティや年間制限はありません。

## <a name="refund-policies"></a>払戻ポリシー

- 12 か月間のローリング ウィンドウ内に合計払戻金額が 50,000 米国ドルを超えることはできません。
- 払戻を処理できるのは、予約の所有者のみです。 [予約を管理できるユーザーを追加または変更する](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)方法を参照してください。
- Microsoft は返金に対して 12% のペナルティを課す権利を留保します。ただし、現在、このペナルティは課金されていません。

## <a name="exchange-a-non-premium-storage-vm-reservation-for-a-premium-storage-reservation"></a>Premium 以外のストレージ VM 予約を Premium Storage 予約と交換する

Premium Storage をサポートしない VM サイズの購入済み予約を、Premium Storage をサポートする対応する VM サイズと交換できます。 たとえば、_F1_ を _F1s_ と交換できます。 交換を行う場合は、[予約の詳細] に移動し、**[交換]** をクリックします。 交換により、予約済みインスタンスの期間のリセットや、新しいトランザクションの作成が行われることはありません。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。

## <a name="next-steps"></a>次の手順

- 予約を管理する方法については、「[Azure の予約の管理](billing-manage-reserved-vm-instance.md)」をご覧ください。
- Azure の予約の詳細については、次の記事を参照してください。
    - [Azure の予約とは](billing-save-compute-costs-reservations.md)
    - [Azure での予約の管理](billing-manage-reserved-vm-instance.md)
    - [予約割引の適用方法について](billing-understand-vm-reservation-charges.md)
    - [従量課金制サブスクリプションの予約使用量について](billing-understand-reserved-instance-usage.md)
    - [エンタープライズ加入契約の予約使用量について](billing-understand-reserved-instance-usage-ea.md)
    - [予約に含まれない Windows ソフトウェアのコスト](billing-reserved-instance-windows-software-costs.md)
    - [パートナー センターのクラウド ソリューション プロバイダー (CSP) プログラムでの Azure の予約](/partner-center/azure-reservations)
