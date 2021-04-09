---
title: 予約容量を使用したコンピューティング コストの節約
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Azure SQL Database および SQL managed Instance の予約容量を購入して計算コストを節約する方法について説明します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 10/13/2020
ms.openlocfilehash: cacd43502a01352c24f8fcfd85b12aac781dccbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98602509"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-sql-database--sql-managed-instance"></a>予約容量を使用してリソースのコストを節約する - Azure SQL Database および SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)] 

コンピューティング リソースの予約をコミットすることで、Azure SQL Database および SQL Managed Instance のコストを従量課金制より節約できます。 予約容量では、SQL Database や SQL Managed Instance の 1 年間または 3 年間の使用を確約することで、コンピューティング コストの大幅な割引を受けることができます。 予約容量を購入するには、Azure リージョン、デプロイの種類、パフォーマンス レベル、および期間を指定する必要があります。

特定のデータベースまたはマネージド インスタンスに予約を割り当てる必要はありません。 一致する既に実行されている既存のデプロイや、新たにデプロイされたデプロイに対して、特典が自動的に適応されます。 予約を購入することで、1 年間または 3 年間のコンピューティング コストの使用を確約したことになります。 予約を購入するとすぐに、予約の属性に一致するコンピューティング料金は従量課金制で課金されなくなります。 

予約は、プライマリのコンピューティング レプリカと課金対象のセカンダリ コンピューティング レプリカの両方に適用されますが、本サービスに関連付けられているソフトウェア、ネットワーク、ストレージの料金には適用されません。 予約期間が満了した時点で、課金特典の有効期限は切れ、従量課金料金がデータベースまたはマネージド インスタンスに適用されます。 予約は自動更新されません。 価格の詳細については、[予約容量オファー](https://azure.microsoft.com/pricing/details/sql-database/managed/)に関するページを参照してください。

予約容量は [Azure portal](https://portal.azure.com) で購入できます。 予約の支払いは、[前払いまたは月払い](../../cost-management-billing/reservations/prepare-buy-reservation.md)で行います。 予約容量を購入するには:

- 少なくとも 1 つのエンタープライズ サブスクリプションまたは従量課金制料金の個々のサブスクリプションで所有者ロールである必要があります。
- Enterprise サブスクリプションの場合、[EA ポータル](https://ea.azure.com)で **[予約インスタンスを追加します]** を有効にする必要があります。 または、その設定が無効になっている場合は、ユーザーはサブスクリプションの EA 管理者である必要があります。 予約容量。

エンタープライズおよび従量課金制のお客様に対する予約購入の課金方法の詳細については、「[エンタープライズ加入契約に適用される Azure の予約の使用状況について](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)」および「[従量課金制サブスクリプションに適用される Azure の予約の使用状況について](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)」をご覧ください。

> [!NOTE]
> 予約容量を購入しても、特定のインフラストラクチャ リソース (仮想マシンまたはノード) が事前に割り当てられたり予約されたりすることはありません。

## <a name="determine-correct-size-before-purchase"></a>購入前に正しいサイズを決定する

予約のサイズは、既存のまたはすぐにデプロイされる予定のデータベースまたはマネージド インスタンス (特定のリージョン内で同じパフォーマンス階層とハードウェア世代を使用するもの) で使用される計算量の合計に基づいて決める必要があります。

たとえば、1 つの汎用の Gen5 - 16 仮想コア エラスティック プールと、2 つのビジネス クリティカルな Gen5 - 4 仮想コア単一データベースを実行しているとします。 さらに、来月には汎用の Gen5 - 16 仮想コア エラスティック プールを 1 つとビジネス クリティカルな Gen5 - 32 仮想コア エラスティック プールを 1 つ追加する予定だとします。 また、少なくとも 1 年間はこれらのリソースが必要になることがわかっているとします。 この場合、単一データベース/エラスティック プールの General Purpose - Gen5 用に 32 (2 x 16) 仮想コア 1 年分と、単一データベース/エラスティック プールの Business Critical - Gen5 用に 40 (2 x 4 + 32) 仮想コア 1 年分を、購入する必要があります。

## <a name="buy-reserved-capacity"></a>予約容量をご購入ください

1. [Azure portal](https://portal.azure.com) にサインインします。
2. **[すべてのサービス]**  >  **[予約]** を選択します。
3. **[追加]** を選択し、 **[購入予約]** ウィンドウで **[SQL Database]** を選択して SQL Database の新しい予約を購入します。
4. 必須フィールドに必要事項を入力します。 選択した属性と一致する、SQL Database および SQL Managed Instance の既存のデータベースが、予約容量の割引を受けられます。 割引を受けるデータベースまたはマネージド インスタンスの実際の数は、選択したスコープと数量によって変わります。

    ![予約容量の購入を送信する前のスクリーンショット](./media/reserved-capacity-overview/sql-reserved-vcores-purchase.png)

    次の表で、必須フィールドについて説明します。
    
    | フィールド      | 説明|
    |------------|--------------|
    |サブスクリプション|容量予約の支払いに使用するサブスクリプション。 サブスクリプションの支払方法に対して、予約の初期コストが課金されます。 サブスクリプションの種類は、Enterprise Agreement (オファー番号: MS-AZR-0017P または MS-AZR-0148P) または従量課金制料金 (オファー番号: MS-AZR-0003P または MS-AZR-0023P) の個別の契約である必要があります。 Enterprise サブスクリプションの場合、登録の Azure 前払い (旧称: 年額コミットメント) の残高から料金が差し引かれるか、超過分として課金されます。 従量課金制料金の個々のサブスクリプションの場合、クレジット カードまたはサブスクリプションの請求書に記載されている支払方法に料金が課金されます。|
    |Scope       |1 つのサブスクリプションまたは複数のサブスクリプション (共有スコープ) を仮想コアの予約のスコープにすることができます。 以下を選択した場合 <br/><br/>**共有** - 仮想コアの予約割引は、課金のコンテキスト内にある任意のサブスクリプションで実行されているデータベースまたはマネージド インスタンスに適用されます。 エンタープライズのお客様の場合、共有スコープが対象の登録であり、登録内のすべてのサブスクリプションが含まれます。 従量課金制のお客様の場合、共有スコープは、アカウント管理者が作成するすべての従量課金制サブスクリプションです。<br/><br/>**単一サブスクリプション** - 仮想コアの予約割引は、このサブスクリプションのデータベースまたはマネージド インスタンスに適用されます。 <br/><br/>**単一のリソース グループ** - 予約割引は、選択したサブスクリプションおよびそのサブスクリプション内の選択したリソース グループ内のデータベースまたはマネージド インスタンスに適用されます。|
    |リージョン      |容量予約の対象となる Azure リージョン。|
    |デプロイの種類|予約を購入する SQL リソースの種類。|
    |パフォーマンス レベル|データベースまたはマネージド インスタンスのサービス レベル。 |
    |期間        |1 年間または 3 年間。|
    |Quantity    |予約容量の予約内で購入されるコンピューティング リソース数。 この数量は、予約し、請求時に割り引きを受ける、選択された Azure リージョンとパフォーマンス レベルに含まれる仮想コアの数です。 たとえば、米国東部リージョンで、合計コンピューティング容量を Gen5 仮想コア 16 個とする複数のデータベースを実行する場合、あるいは実行する予定の場合、すべてのデータベースを最大限に活用するため、数量に 16 を指定します。 |

1. 予約容量の予約コストについては、「**コスト**」セクションを参照してください。
1. **[購入]** を選択します。
1. **[この予約を表示する]** を選択すると、購入の状態が表示されます。

## <a name="cancel-exchange-or-refund-reservations"></a>予約の取り消し、交換、または返金

一定の制限付きで、予約の取り消し、交換、または返金を行うことができます。 詳しくは、「[Azure の予約のセルフサービスによる交換と払戻](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)」を参照してください。

## <a name="vcore-size-flexibility"></a>仮想コアのサイズの柔軟性

同じパフォーマンス レベルとリージョン内であれば、予約容量のベネフィットを失うことなく、仮想コアのサイズを柔軟にスケールアップまたはスケールダウンできます。 また、予約容量を使用すると、予約容量の特典を失うことなく、通常の操作の一環として、(同じリージョン内およびパフォーマンス レベルで) ホット データベースをエラスティック プールから一時的に出したり入れたりする柔軟性が確保されます。 予約に未適用バッファーを維持することにより、予算を超えることなく、パフォーマンスの急上昇を効果的に管理できます。

## <a name="limitation"></a>制限事項

SQL Database で DTU ベース (Basic、Standard、または Premium) のデータベースを予約することはできません。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。

## <a name="next-steps"></a>次のステップ

仮想コアの予約割引は、容量予約の範囲と属性に一致するデータベースまたはマネージド インスタンスの数に自動的に適用されます。 容量予約のスコープは、[Azure portal](https://portal.azure.com)、PowerShell、Azure CLI、または API で更新できます。

容量予約を管理する方法については、[予約容量の管理](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)に関するページを参照してください。

Azure の予約の詳細については、次の記事を参照してください。

- [Azure の予約とは](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Azure の予約の管理](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Azure の予約割引を理解する](../../cost-management-billing/reservations/understand-reservation-charges.md)
- [従量課金制サブスクリプションの予約使用量について](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約使用量について](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [パートナー センターのクラウド ソリューション プロバイダー (CSP) プログラムでの Azure の予約](/partner-center/azure-reservations)