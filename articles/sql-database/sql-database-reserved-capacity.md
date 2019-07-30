---
title: 前払いで Azure SQL Database 仮想コアのコストを削減する | Microsoft Docs
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
manager: craigg
ms.date: 07/01/2019
ms.openlocfilehash: 8c99ae1aa96e41173fb6e4d7303979d8fc167003
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491617"
---
# <a name="prepay-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Azure SQL Database の予約容量を使用した SQL Database 計算リソースの前払い

計算リソースを前払いすることで、従量課金制より Azure SQL Database に関するコストを節約できます。 Azure SQL Database の予約容量を使用すると、SQL Database を 1 年間または 3 年間分を前払いすることで計算コストを大幅に引き下げることができます。 SQL Database の予約容量を購入するには、Azure リージョン、デプロイの種類、パフォーマンス レベル、および期間を指定する必要があります。


特定の SQL Database インスタンス (単一データベース、エラスティック プール、またはマネージド インスタンス) に予約を割り当てる必要はありません。 既に実行している SQL Database インスタンスまたは新しくデプロイされた SQL Database インスタンスには、この特典が自動的に適用されます。 予約を購入すると、計算コストを 1 年間または 3 年間分前払いすることになります。 予約を購入するとすぐに、予約の属性に一致する SQL Database のコンピューティング料金は従量課金制で課金されなくなります。 予約には、SQL データベース インスタンスに関連するソフトウェア、ネットワーク、またはストレージの料金は含まれません。 予約期間が満了した時点で、課金特典の有効期限は切れ、従量課金料金が SQL データベースに適用されます。 予約は自動更新されません。 価格の詳細については、[SQL Database の予約容量オファー](https://azure.microsoft.com/pricing/details/sql-database/managed/)に関するページを参照してください。

Azure SQL Database の予約容量は、[Azure portal](https://portal.azure.com) で購入できます。 SQL Database の予約容量を購入するには:

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
3. **[追加]** を選択し、[製品の種類を選択してください] ウィンドウで **[SQL Database]** を選択して、SQL Database の新しい予約を購入します。
4. 必須フィールドに入力します。 選択した属性と一致する既存または新規の単一データベース、エラスティック プール、マネージド インスタンスが、予約容量の割引を受けられます。 割引を受ける SQL Database インスタンスの実際の数は、選択したスコープと数量によって変わります。

   ![SQL Database の予約容量の購入を送信する前のスクリーンショット](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

    | フィールド      | 説明|
    |:------------|:--------------|
    |Name        |この予約の名前。|
    |サブスクリプション|SQL Database の予約容量の予約の支払いに使用するサブスクリプション。 サブスクリプションの支払方法に対して、SQL Database の予約容量の予約の前払いコストが課金されます。 サブスクリプションの種類は、マイクロソフト エンタープライズ契約 (プラン番号:MS-AZR-0017P または MS-AZR-0148P) または従量課金制料金の個々の契約 (プラン番号:MS-AZR-0003P または MS-AZR-0023P)。 エンタープライズ サブスクリプションの場合、登録の年額コミットメント残高から料金が差し引かれるか、超過料金として課金されます。 従量課金制料金の個々のサブスクリプションの場合、クレジット カードまたはサブスクリプションの請求書に記載されている支払方法に料金が課金されます。|
    |Scope (スコープ)       |1 つのサブスクリプションまたは複数のサブスクリプション (共有スコープ) を仮想コアの予約のスコープにすることができます。 以下を選択した場合: <br/><br/>**単一サブスクリプション** - 仮想コアの予約割引はこのサブスクリプションの SQL Database インスタンスに適用されます。 <br/><br/>**共有サブスクリプション** - 仮想コアの予約割引は、課金のコンテキスト内にある任意のサブスクリプションで実行されている SQL Database インスタンスに適用されます。 エンタープライズのお客様の場合、共有スコープが対象の登録であり、登録内のすべてのサブスクリプションが含まれます。 従量課金制のお客様の場合、共有スコープは、アカウント管理者が作成するすべての従量課金制サブスクリプションです。|
    |リージョン      |SQL Database 予約容量の予約で充当されない Azure リージョン。|
    |デプロイの種類|予約を購入する SQL リソースの種類。|
    |パフォーマンス レベル|SQL Database インスタンスのサービス レベル。
    |期間        |1 年間または 3 年間。|
    |Quantity    |SQL Database の予約容量の予約内で購入されるインスタンス数。 数量は、課金の割引を受けられる実行中の SQL Database インスタンス数です。 たとえば、米国東部で SQL Database インスタンスを 10 個実行している場合、実行中のすべてのマシンのメリットを最大限に利用するには、数量を 10 と指定します。 |
    |||

5. **[コスト]** セクションで、SQL Database の予約容量の予約コストを確認します。
6. **[購入]** を選択します。
7. **[この予約を表示する]** を選択すると、購入の状態が表示されます。

## <a name="cancellations-and-exchanges"></a>キャンセルと交換

SQL Database の予約容量の予約をキャンセルする必要がある場合は、12% の中途解約料が発生する可能性があります。 払い戻し額は、購入価格または予約の現在の価格のいずれかの最低価格に基づいています。 払い戻し額の上限は年額 50,000 ドルです。 日割り計算によって算出された金額から 12% の中途解約料を差し引いた金額が払い戻されます。 キャンセルを要求するには、Azure portal で予約に移動し、 **[返金]** を選択して、サポート要求を作成します。

SQL Database の予約容量の予約を別のリージョン、デプロイの種類、パフォーマンス レベル、または期間に変更する必要がある場合は、同等以上の価値がある別の予約に交換できます。 新しい予約期間の開始日は、元の予約からは引き継がれません。 1 年または 3 年の予約期間は、新しい予約を作成した時点から始まります。 交換を要求するには、Azure portal で予約に移動し、 **[交換]** を選択して、サポート要求を作成します。

予約を交換または返金する方法の詳細については、[予約の交換と返金](../billing/billing-azure-reservations-self-service-exchange-and-refund.md)に関するページを参照してください。

## <a name="vcore-size-flexibility"></a>仮想コアのサイズの柔軟性

同じパフォーマンス レベルとリージョン内であれば、予約容量のベネフィットを失うことなく、仮想コアのサイズを柔軟にスケールアップまたはスケールダウンできます。 また、SQL Database の予約容量では、通常の操作 (同じリージョン内および同じパフォーマンス レベル内) の一部として、予約容量のベネフィットを失うことなく、プールと単一のデータベースの間で、ホット データベースを一時的に柔軟に移動できます。 予約内の未適用バッファーを維持することにより、予算を超えずに、パフォーマンスの急増を効果的に管理できます。

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
