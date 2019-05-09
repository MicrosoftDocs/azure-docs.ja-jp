---
title: SUSE Linux プランの購入 - Azure の予約 | Microsoft Docs
description: SUSE の使用料を前払いして従量課金制のコストより費用を節約する方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/18/2019
ms.author: yashar
ms.openlocfilehash: 4f70a34febcf0b39d051053a6ddd9abe5c9a6726
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2019
ms.locfileid: "55745981"
---
# <a name="prepay-for-suse-software-plans-from-azure-reservations"></a>Azure の予約からの SUSE ソフトウェア プランの前払い

SUSE の使用料を前払いすると、従量課金制のコストより費用を節約できます。 この割引は SUSE 測定にのみ適用され、仮想マシンの使用には適用されません。 仮想マシンの予約を別個に購入することで、さらに節約できます。

SUSE ソフトウェア プランは Azure portal で購入できます。 プランを購入するには:

- 少なくとも 1 つのエンタープライズ サブスクリプションまたは従量課金制サブスクリプションで所有者ロールである必要があります。
- Enterprise サブスクリプションの場合、[EA ポータル](https://ea.azure.com)で **[予約インスタンスを追加します]** を有効にする必要があります。 または、その設定が無効になっている場合は、ユーザーはサブスクリプションの EA 管理者である必要があります。
- クラウド ソリューション プロバイダー (CSP) プログラムの場合、管理エージェントまたはセールス エージェントが SUSE プランを購入できます。

## <a name="buy-a-suse-software-plan"></a>SUSE ソフトウェア プランの購入

1. Azure portal で [[予約]](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) に移動します。
1. **[追加]** を選択し、SUSE Linux を選択します。
1. 必須フィールドに必要事項を入力します。 購入する属性に一致するすべての SUSE Linux VM に割引が適用されます。 割引が適用されるデプロイの実際の数は、選択したスコープと数量によって変わります。

    | フィールド      | 説明|
    |:------------|:--------------|
    |Name        |この購入の名前。|
    |サブスクリプション|このプランの支払いに使用するサブスクリプション。 サブスクリプションの支払方法に対して、予約の初期コストが課金されます。 サブスクリプションの種類は、マイクロソフト エンタープライズ契約 (プラン番号:MS-AZR-0017P または MS-AZR-0148P) または従量課金制 (プラン番号:MS-AZR-0003P または MS-AZR-0023P)。 エンタープライズ サブスクリプションの場合、登録の年額コミットメント残高から料金が差し引かれるか、超過料金として課金されます。 従量課金制サブスクリプションの場合、クレジット カードまたはサブスクリプションの請求書に記載されている支払方法に料金が課金されます。|
    |Scope (スコープ)       |1 つのサブスクリプションまたは複数のサブスクリプション (共有スコープ) をスコープにすることができます。 以下を選択した場合: <ul><li>[単一サブスクリプション] - プランの割引は、このサブスクリプションの SUSE Linux の使用に適用されます。 </li><li>[共有] - プランの割引は、課金のコンテキスト内にあるすべてのサブスクリプションの SUSE Linux の使用に適用されます。 エンタープライズのお客様の場合、共有スコープが対象の登録であり、登録内のすべてのサブスクリプションが含まれます。 従量課金制のお客様の場合、共有スコープは、アカウント管理者が作成するすべての従量課金制サブスクリプションです。</li></ul>|
    |ソフトウェア プラン     |SUSE Linux プランを選択します。 購入するものを決める際の参考として、[SUSE Linux Enterprise ソフトウェアの予約割引の適用方法](../../billing/billing-understand-suse-reservation-charges.md)に関するページを参照してください。|
    |VM サイズ     |SUSE Linux の料金は、VM 上の vCPU の数によって異なります。 SUSE Linux VM 上の vCPU の数を表すオプションを選択します。|
    |用語        |1 年間または 3 年間。|
    |数量    |この SUSE Linux プランを購入する VM の数。 数量は、課金の割引を受けられる実行中の SUSE Linux インスタンス数です。|
1. **[購入]** を選択します。
1. **[この予約を表示する]** を選択すると、購入の状態が表示されます。

予約割引は、予約に一致するすべての実行中の SUSE 仮想マシンに対して自動的に適用されます。 この割引は SUSE 測定にのみ適用されます。 VM の計算料金はこのプランの対象外です。

## <a name="discount-applies-to-different-vm-sizes-with-instance-size-flexibility"></a>さまざまな VM サイズに適用され、インスタンス サイズの柔軟性を提供する割引

予約 VM インスタンスと同様に、SUSE Linux プランは、インスタンス サイズの柔軟性を提供します。 つまり、購入した SUSE プランとは異なるサイズの VM をデプロイする場合でも、割引が適用されます。 詳細については、[SUSE Linux Enterprise ソフトウェアの予約割引が適用されるしくみ](../../billing/billing-understand-suse-reservation-charges.md)に関するページを参照してください。

## <a name="cancellation-and-exchanges-not-allowed"></a>キャンセルと交換はできません

購入した SUSE プランのキャンセルまたは交換はできません。 適切なプランを購入するためには、使用状況を確認してください。 購入するものを決める際の参考として、[SUSE Linux Enterprise ソフトウェアの予約割引の適用方法](../../billing/billing-understand-suse-reservation-charges.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

予約を管理する方法については、「[Azure の予約の管理](../../billing/billing-manage-reserved-vm-instance.md)」をご覧ください。

詳細については、次の記事を参照してください。

- [Azure の予約とは](../../billing/billing-save-compute-costs-reservations.md)
- [Azure での予約の管理](../../billing/billing-manage-reserved-vm-instance.md)
- [SUSE の予約割引の適用方法について](../../billing/billing-understand-suse-reservation-charges.md)
- [従量課金制サブスクリプションの予約使用量について](../../billing/billing-understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約使用量について](../../billing/billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。