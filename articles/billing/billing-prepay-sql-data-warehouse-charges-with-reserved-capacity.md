---
title: Azure の容量が予約された SQL Data Warehouse 料金を前払いする | Microsoft Docs
description: コスト削減のために容量が予約された SQL Data Warehouse 料金を前払いする方法について説明します。
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: banders
ms.openlocfilehash: cea2c8e6d476c3ea2799337ab2da1f9406731814
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565352"
---
# <a name="prepay-for-sql-data-warehouse-charges-with-reserved-capacity"></a>容量が予約された SQL Data Warehouse 料金の前払い

1 年間または 3 年間の cDWU 使用状況について前払いすることで、Azure SQL Data Warehouse にかかるコストを削減することができます。 SQL Data Warehouse の予約容量を購入するには、Azure リージョンと期間を選択する必要があります。 その後、SQL Data Warehouse SKU をカートに追加して、購入する cDWU ユニットの数量を選択します。

予約を購入すると、予約の属性に一致する SQL Data Warehouse 使用状況は従量課金制で課金されなくなります。

予約には、SQL Data Warehouse 使用状況に関連するストレージまたはネットワーク料金は含まれません。

予約容量を超過した場合、SQL Data Warehouse インスタンスは引き続き実行されますが、従量課金制の料金で課金されます。 予約は自動更新されません。

価格の詳細については、[SQL Data Warehouse の予約容量オファー](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)に関するページを参照してください。

Azure SQL Data Warehouse の予約容量は、[Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) で購入できます。 予約容量を購入するには:

- 少なくとも 1 つのエンタープライズ サブスクリプションまたは従量課金制サブスクリプションで所有者ロールを所持している必要があります。
- エンタープライズ サブスクリプションの場合、[EA ポータル](https://ea.azure.com/)で **[予約インスタンスを追加します]** を有効にする必要があります。 その設定が無効になっている場合は、EA 管理者である必要があります。
- クラウド ソリューション プロバイダー (CSP) プログラムの場合、管理者エージェントまたはセールス エージェントのみが SQL Data Warehouse の予約容量を購入できます。

エンタープライズおよび従量課金制のお客様に対する予約購入の課金方法の詳細については、「[エンタープライズ加入契約に適用される Azure の予約の使用状況について](billing-understand-reserved-instance-usage-ea.md)」および「[従量課金制サブスクリプションに適用される Azure の予約の使用状況について](billing-understand-reserved-instance-usage.md)」をご覧ください。

## <a name="choose-the-right-size-before-purchase"></a>購入する前に適切なサイズを選択する

SQL Data Warehouse の予約サイズは、使用するコンピューティング Data Warehouse ユニット (cDWU) の合計に基づく必要があります。 購入は、100 cDWU 単位で行われます。

たとえば、SQL Data Warehouse の合計使用量が DW3000c とします。 そのすべてに対して予約容量を購入したいとします。 そのためには、cDWU 予約容量を 30 ユニット購入する必要があります。

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>SQL Data Warehouse の予約容量を購入する

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. **[すべてのサービス]**  >  **[予約]** を選択します。
3. サブスクリプションを選択します。 [サブスクリプション] リストを使用して、予約容量の支払いに使用するサブスクリプションを選択します。 サブスクリプションの支払方法に対して、予約容量の初期コストが課金されます。 サブスクリプションの種類は、マイクロソフト エンタープライズ契約 (プラン番号:MS-AZR-0017P または MS-AZR-0148P) または従量課金制 (プラン番号:MS-AZR-0003P または MS-AZR-0023P)。
  - エンタープライズ サブスクリプションの場合、登録の年額コミットメント残高から料金が差し引かれるか、超過料金として課金されます。
  - 従量課金制サブスクリプションの場合、クレジット カードまたはサブスクリプションの請求書に記載されている支払方法に料金が課金されます。
4. スコープを選択します。 [スコープ] リストを使用して、サブスクリプション スコープを選択します。
  - **単一のリソース グループのスコープ** — 選択されたリソース グループ内の一致するリソースにのみ、予約割引を適用します。
  - **単一サブスクリプション** — 選択されたサブスクリプションの一致するリソースに予約割引を適用します。
  - **共有スコープ** — 課金コンテキスト内にある有効なサブスクリプションの一致するリソースに予約割引を適用します。 マイクロソフト エンタープライズ契約のお客様の場合、課金コンテキストは登録です。 従量課金制料金の個々のサブスクリプションの場合、課金スコープはアカウント管理者によって作成されるすべての有効なサブスクリプションです。
    - エンタープライズのお客様の場合、課金コンテキストは EA 登録です。
    - 従量課金制のお客様の場合、共有スコープは、アカウント管理者が作成するすべての従量課金制サブスクリプションです。
5. リージョンを選択して、予約容量でカバーされる Azure リージョンを選びます。
6. 数量を選択します。 購入する 100 Data Warehouse ユニット (cDWU) の数量を入力します。    
  たとえば、数量として 30 を入力すると、毎時間 3,000 cDWU の予約容量が割り当てられることになります。
7. **[コスト]** セクションで、SQL Data Warehouse の予約容量の予約コストを確認します。
8. **[購入]** を選択します。
9. **[この予約を表示する]** を選択すると、購入の状態が表示されます。

## <a name="cancellations-and-exchanges"></a>キャンセルと交換

SQL Data Warehouse の予約容量をキャンセルする必要がある場合は、12% の中途解約料が発生する可能性があります。 払い戻し額は、購入価格または予約の現在の価格のいずれかの最低価格に基づいています。 払い戻し額の上限は年額 50,000.00 ドルです。 日割り計算によって算出された金額から 12% の中途解約料を差し引いた金額が払い戻されます。 キャンセルを要求するには、Azure portal で予約に移動し、 **[返金]** を選択して、サポート要求を作成します。

ご自身の SQL Data Warehouse 予約容量を別のリージョンまたは期間に変更する必要がある場合は、同等以上の価値がある別の予約に交換できます。 新しい予約期間の開始日は、元の予約からは引き継がれません。 1 年または 3 年の予約期間は、新しい予約を作成した時点から始まります。 交換を要求するには、Azure portal で予約を開き、 **[交換]** を選択して、サポート要求を作成します。

予約を交換または返金する方法の詳細については、[予約の交換と返金](billing-azure-reservations-self-service-exchange-and-refund.md)に関するページを参照してください。

予約割引は、SQL Data Warehouse の予約容量のスコープとリージョンに一致する複数の SQL Data Warehouse インスタンスに自動的に適用されます。 SQL Data Warehouse の予約容量のスコープは、[Azure portal](https://portal.azure.com/)、PowerShell、CLI、または API で更新できます。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://portal.azure.com/)してください。

## <a name="next-steps"></a>次の手順

- 予約割引の Azure SQL Data Warehouse への適用方法の詳細については、[Azure SQL Data Warehouse への予約割引の適用方法](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md)に関する記事を参照してください。

- Azure の予約の詳細については、次の記事を参照してください。
  - [Azure の予約とは](billing-save-compute-costs-reservations.md)
  - [Azure の予約の管理](billing-manage-reserved-vm-instance.md)
  - [Azure の予約割引を理解する](billing-understand-reservation-charges.md)
  - [従量課金制サブスクリプションの予約使用量について](billing-understand-reserved-instance-usage.md)
  - [エンタープライズ加入契約の予約使用量について](billing-understand-reserved-instance-usage-ea.md)
