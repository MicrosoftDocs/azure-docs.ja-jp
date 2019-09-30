---
title: Azure SQL Database 仮想コアのコストを削減する | Microsoft Docs
description: Azure SQL Database の予約容量を購入して計算コストを節約する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 08/29/2019
ms.openlocfilehash: 5ab5481a89f7bb9c74133487b01879b00b7def32
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806579"
---
# <a name="save-costs-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Azure SQL Database の予約容量を使用して SQL Database コンピューティング リソースのコストを削減する

コンピューティング リソースの予約をコミットすることで、Azure SQL Database のコストを従量課金制より節約できます。 Azure SQL Database の予約容量では、SQL Database の 1 年間または 3 年間の使用をコミットすることで、コンピューティング コストを大幅に引き下げることができます。 SQL Database の予約容量を購入するには、Azure リージョン、デプロイの種類、パフォーマンス レベル、および期間を指定する必要があります。


特定の SQL Database インスタンス (単一データベース、エラスティック プール、またはマネージド インスタンス) に予約を割り当てる必要はありません。 既に実行している SQL Database インスタンスまたは新しくデプロイされた SQL Database インスタンスには、この特典が自動的に適用されます。 予約を購入することで、1 年間または 3 年間のコンピューティング コストの使用をコミットしたことになります。 予約を購入するとすぐに、予約の属性に一致する SQL Database のコンピューティング料金は従量課金制で課金されなくなります。 予約には、SQL データベース インスタンスに関連するソフトウェア、ネットワーク、またはストレージの料金は含まれません。 予約期間が満了した時点で、課金特典の有効期限は切れ、従量課金料金が SQL データベースに適用されます。 予約は自動更新されません。 価格の詳細については、[SQL Database の予約容量オファー](https://azure.microsoft.com/pricing/details/sql-database/managed/)に関するページを参照してください。

Azure SQL Database の予約容量は、[Azure portal](https://portal.azure.com) で購入できます。 予約の支払いは、[前払いまたは月払い](../billing/billing-monthly-payments-reservations.md)で行います。 SQL Database の予約容量を購入するには:

- 少なくとも 1 つのエンタープライズ サブスクリプションまたは従量課金制料金の個々のサブスクリプションで所有者ロールである必要があります。
- Enterprise サブスクリプションの場合、[EA ポータル](https://ea.azure.com)で **[予約インスタンスを追加します]** を有効にする必要があります。 または、その設定が無効になっている場合は、ユーザーはサブスクリプションの EA 管理者である必要があります。
- クラウド ソリューション プロバイダー (CSP) プログラムの場合、管理者エージェントまたはセールス エージェントのみが SQL Database の予約容量を購入できます。

エンタープライズおよび従量課金制のお客様に対する予約購入の課金方法の詳細については、「[エンタープライズ加入契約に適用される Azure の予約の使用状況について](../billing/billing-understand-reserved-instance-usage-ea.md)」および「[従量課金制サブスクリプションに適用される Azure の予約の使用状況について](../billing/billing-understand-reserved-instance-usage.md)」をご覧ください。

## <a name="determine-the-right-sql-size-before-purchase"></a>購入する前に適切な SQL サイズを決定する

特定のリージョン内で、同じパフォーマンス レベルとハードウェア世代を使用する、既存またはすぐにデプロイされる予定の単一データベース、エラスティック プール、またはマネージド インスタンスで使用される計算の総量に基づいて、予約のサイズを決める必要があります。

たとえば、1 つの汎用の Gen5 - 16 仮想コア エラスティック プールと、2 つのビジネス クリティカルな Gen5 - 4 仮想コア単一データベースを実行しているとします。 さらに、来月には汎用の Gen5 - 16 仮想コア エラスティック プールを 1 つとビジネス クリティカルな Gen5 - 32 仮想コア エラスティック プールを 1 つ追加する予定だとします。 また、少なくとも 1 年間はこれらのリソースが必要になることがわかっているとします。 この場合、単一データベース/エラスティック プールの General Purpose - Gen5 用に 32 (2 x 16) 仮想コア 1 年分と、単一データベース/エラスティック プールの Business Critical - Gen5 用に 40 (2 x 4 + 32) 仮想コア 1 年分を、購入する必要があります。

## <a name="buy-sql-database-reserved-capacity"></a>SQL Database の予約容量の購入

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[すべてのサービス]**  >  **[予約]** を選択します。
3. **[追加]** を選択し、[購入予約] ウィンドウで **[SQL Database]** を選択して SQL Database の新しい予約を購入します。
4. 必須フィールドに入力します。 選択した属性と一致する既存または新規の単一データベース、エラスティック プール、マネージド インスタンスが、予約容量の割引を受けられます。 割引を受ける SQL Database インスタンスの実際の数は、選択したスコープと数量によって変わります。
    ![SQL Database の予約容量の購入を送信する前のスクリーンショット](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

次の表で、必須フィールドについて説明します。

| フィールド      | 説明|
|------------|--------------|
|Subscription|SQL Database の予約容量の予約の支払いに使用するサブスクリプション。 サブスクリプションの支払方法に対して、SQL Database の予約容量の予約の前払いコストが課金されます。 サブスクリプションの種類は、マイクロソフト エンタープライズ契約 (プラン番号:MS-AZR-0017P または MS-AZR-0148P) または従量課金制料金の個々の契約 (プラン番号:MS-AZR-0003P または MS-AZR-0023P)。 エンタープライズ サブスクリプションの場合、登録の年額コミットメント残高から料金が差し引かれるか、超過料金として課金されます。 従量課金制料金の個々のサブスクリプションの場合、クレジット カードまたはサブスクリプションの請求書に記載されている支払方法に料金が課金されます。|
|Scope (スコープ)       |1 つのサブスクリプションまたは複数のサブスクリプション (共有スコープ) を仮想コアの予約のスコープにすることができます。 以下を選択した場合: <br/><br/>**共有** - 仮想コアの予約割引は、課金のコンテキスト内にある任意のサブスクリプションで実行されている SQL Database インスタンスに適用されます。 エンタープライズのお客様の場合、共有スコープが対象の登録であり、登録内のすべてのサブスクリプションが含まれます。 従量課金制のお客様の場合、共有スコープは、アカウント管理者が作成するすべての従量課金制サブスクリプションです。<br/><br/>**単一サブスクリプション** - 仮想コアの予約割引はこのサブスクリプションの SQL Database インスタンスに適用されます。 <br/><br/>**[1 つのリソース グループ]** - 予約割引は、選択したサブスクリプションおよびそのサブスクリプション内の選択したリソース グループ内の SQL Database インスタンスに適用されます。|
|リージョン      |SQL Database 予約容量の予約で充当されない Azure リージョン。|
|デプロイの種類|予約を購入する SQL リソースの種類。|
|パフォーマンス レベル|SQL Database インスタンスのサービス レベル。
|期間        |1 年間または 3 年間。|
|Quantity    |SQL Database の予約容量の予約内で購入されるコンピューティング リソース数。 この数量は、予約し、請求時に割り引きを受ける、選択された Azure リージョンとパフォーマンス レベルに含まれる仮想コアの数です。 たとえば、米国東部リージョンで、合計コンピューティング容量を Gen5 仮想コア 16 個とする SQL Database を実行している場合、あるいは実行する予定の場合、すべてのインスタンスを最大限に活用するため、数量に 16 を指定します。 |

1. **[コスト]** セクションで、SQL Database の予約容量の予約コストを確認します。
1. **[購入]** を選択します。
1. **[この予約を表示する]** を選択すると、購入の状態が表示されます。

## <a name="cancel-exchange-or-refund-reservations"></a>予約の取り消し、交換、または返金

一定の制限付きで、予約の取り消し、交換、または返金を行うことができます。 詳しくは、「[Azure の予約のセルフサービスによる交換と払戻](../billing/billing-azure-reservations-self-service-exchange-and-refund.md)」を参照してください。

## <a name="vcore-size-flexibility"></a>仮想コアのサイズの柔軟性

同じパフォーマンス レベルとリージョン内であれば、予約容量のベネフィットを失うことなく、仮想コアのサイズを柔軟にスケールアップまたはスケールダウンできます。 また、SQL Database の予約容量では、通常の操作 (同じリージョン内および同じパフォーマンス レベル内) の一部として、予約容量のベネフィットを失うことなく、プールと単一のデータベースの間で、ホット データベースを一時的に柔軟に移動できます。 予約内の未適用バッファーを維持することにより、予算を超えずに、パフォーマンスの急増を効果的に管理できます。

## <a name="limitation"></a>制限事項

DTU ベース (Basic、Standard、または Premium) の SQL データベースを予約することはできません。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。

## <a name="next-steps"></a>次の手順

仮想コアの予約割引は、SQL Database の予約容量の予約スコープと属性に一致する複数の SQL Database インスタンスに自動的に適用されます。 SQL Database の予約容量の予約スコープは、[Azure portal](https://portal.azure.com)、PowerShell、CLI、または API で更新できます。

SQL Database の予約容量の予約を管理する方法については、[SQL Database の予約容量の管理](../billing/billing-manage-reserved-vm-instance.md)に関するページを参照してください。

Azure の予約の詳細については、次の記事を参照してください。

- [Azure の予約とは](../billing/billing-save-compute-costs-reservations.md)
- [Azure の予約の管理](../billing/billing-manage-reserved-vm-instance.md)
- [Azure の予約割引を理解する](../billing/billing-understand-reservation-charges.md)
- [従量課金制サブスクリプションの予約使用量について](../billing/billing-understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約使用量について](../billing/billing-understand-reserved-instance-usage-ea.md)
- [パートナー センターのクラウド ソリューション プロバイダー (CSP) プログラムでの Azure の予約](https://docs.microsoft.com/partner-center/azure-reservations)
