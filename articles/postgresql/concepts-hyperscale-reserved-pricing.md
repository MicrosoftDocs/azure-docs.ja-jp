---
title: 予約済みコンピューティングの価格 - Azure Database for PostgreSQL - Hyperscale (Citus)
description: 予約容量を使用して Azure Database for PostgreSQL - Hyperscale (Citus) コンピューティング リソースを前払いします。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: 35d5d196eccb222bf17e0a129fe4e4041b1f6053
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98600736"
---
# <a name="prepay-for-azure-database-for-postgresql---hyperscale-citus-compute-resources-with-reserved-capacity"></a>予約容量を使用して Azure Database for PostgreSQL - Hyperscale (Citus) コンピューティング リソースを前払いする

Azure Database for PostgreSQL - Hyperscale (Citus) ではコンピューティング リソースを前払いすることで、従量課金制よりコストを節約できるようになりました。 Hyperscale (Citus) の予約容量を使用すると、Hyperscale (Citus) サーバー グループを 1 年分または 3 年分前払いすることでコンピューティング コストを大幅に引き下げることができます。 Hyperscale (Citus) の予約容量を購入するには、Azure リージョン、予約期間、請求頻度を指定する必要があります。

> [!IMPORTANT]
> この記事では、Azure Database for PostgreSQL - Hyperscale (Citus) の予約容量について説明します。 Azure Database for PostgreSQL – Single Server の予約容量の詳細については、「[Azure Database for PostgreSQL の前払い - 予約容量を持つ Single Server 計算リソース](./concept-reserved-pricing.md)」を参照してください。

特定の Hyperscale (Citus) サーバー グループに予約を割り当てる必要はありません。 既に実行している Hyperscale (Citus) サーバー グループまたは新しくデプロイされたものには、予約価格の特典が自動的に適用されます。 予約を購入すると、コンピューティング コストを 1 年間または 3 年間分前払いすることになります。 予約を購入するとすぐに、予約の属性に一致する Hyperscale (Citus) のコンピューティング料金は従量課金制で課金されなくなります。 

予約には、Hyperscale (Citus) サーバー グループに関連するソフトウェア、ネットワーク、またはストレージの料金は含まれません。 予約期間が満了した時点で、課金特典の有効期限は切れ、従量課金料金が Hyperscale (Citus) サーバー グループに適用されます。 予約は自動更新されません。 価格の詳細については、[Azure Database for PostgreSQL - Hyperscale (Citus) の予約容量オファー](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)に関するページを参照してください。

Hyperscale (Citus) の予約容量は [Azure portal](https://portal.azure.com/) で購入できます。 予約の支払いは、[前払いまたは月払い](../cost-management-billing/reservations/prepare-buy-reservation.md)で行います。 予約容量を購入するには:

* 少なくとも 1 つのマイクロソフトエンタープライズ契約 (EA) または従量課金制料金の個々のサブスクリプションで所有者ロールである必要があります。
* マイクロソフトエンタープライズ契約の場合、[EA ポータル](https://ea.azure.com/)で **[予約インスタンスを追加します]** を有効にする必要があります。 または、その設定が無効になっている場合は、ユーザーはサブスクリプションのマイクロソフトエンタープライズ契約管理者である必要があります。
* クラウド ソリューション プロバイダー (CSP) プログラムの場合、管理者エージェントまたはセールス エージェントのみが Hyperscale (Citus) の予約容量を購入できます。

マイクロソフトエンタープライズ契約のお客様と従量課金制のお客様が予約を購入した場合の課金方法については、以下を参照してください。
- [マイクロソフトエンタープライズ契約に適用される Azure の予約の使用状況について](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [従量課金制サブスクリプションに適用される Azure の予約の使用状況について](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="determine-the-right-server-group-size-before-purchase"></a>購入する前に適切なサーバー グループのサイズを決定する

予約のサイズは、既存またはすぐにデプロイされる予定のコーディネーター ノードと、特定のリージョン内の Hyperscale (Citus) サーバー グループのワーカー ノードによって使用される計算量に基づきます。

たとえば、16 仮想コア コーディネーター ノードと 3 つの 8 仮想コア ワーカー ノードで 1 つの Hyperscale (Citus) サーバー グループを実行しているとします。 さらに、32 仮想コア コーディネーター ノードと 2 つの 4 仮想コア ワーカー ノードを使用して、翌月に追加の Hyperscale (Citus) サーバー グループをデプロイする予定があるとします。 また、少なくとも 1 年間はこれらのリソースが必要になるとします。

この場合は、以下に対して 1 年間の予約を購入します。

* 合計 16 個の仮想コア + 32 個の仮想コア = コーディネーター ノード用の 48 個の仮想コア
* 合計 3 ノード x 8 個の仮想コア + 2 ノード x 4 個の仮想コア = 24 + 8 = 32 個のワーカー ノード用の仮想コア

## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Azure Database for PostgreSQL の予約容量を購入する

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. **[すべてのサービス]**  >  **[予約]** を選択します。
1. **[追加]** を選択します。 **[購入予約]** ペインで **[Azure Database for PostgreSQL]** を選択して PostgreSQL データベースの新しい予約を購入します。
1. 購入する **Hyperscale (Citus) のコンピューティング** の種類を選択し、 **[選択]** をクリックします。
1. **[Products]\(製品\)** タブで、選択したコンピューティングの種類の数量を確認します。
1. **[Buy + Review]\(購入と確認\)** タブに進み、購入を完了します。

次の表で、必須フィールドについて説明します。

| フィールド        | 説明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| サブスクリプション | Azure Database for PostgreSQL の予約容量の予約の支払いに使用するサブスクリプション。 サブスクリプションの支払方法に対して、Azure Database for PostgreSQL の予約容量の予約の前払いコストが課金されます。 サブスクリプションの種類は、マイクロソフト エンタープライズ契約 (プラン番号: MS-AZR-0017P または MS-AZR-0148P) または従量課金制料金の個々の契約 (プラン番号:MS-AZR-0003P または MS-AZR-0023P)。 Enterprise Agreement サブスクリプションの場合、登録の Azure 前払い (旧称: 年額コミットメント) の残高から料金が差し引かれるか、超過分として課金されます。 従量課金制料金の個々のサブスクリプションの場合、クレジット カードまたはサブスクリプションの請求書に記載されている支払方法に料金が課金されます。                                                                                  |
| Scope        | 1 つのサブスクリプションまたは複数のサブスクリプション (共有スコープ) を仮想コアの予約のスコープにすることができます。 **[共有]** を選択した場合、仮想コアの予約割引は、課金のコンテキスト内にある任意のサブスクリプションで実行されている Hyperscale (Citus) サーバー グループに適用されます。 マイクロソフトエンタープライズ契約のお客様の場合、共有スコープが対象の登録であり、登録内のすべてのサブスクリプションが含まれます。 従量課金制のお客様の場合、共有スコープは、アカウント管理者が作成するすべての従量課金制サブスクリプションです。 **[1 つのサブスクリプション]** を選択した場合、仮想コアの予約割引はこのサブスクリプションの Hyperscale (Citus) サーバー グループに適用されます。 **[1 つのリソース グループ]** を選択した場合、予約割引は、選択したサブスクリプションおよびそのサブスクリプション内の選択したリソース グループ内の Hyperscale (Citus) サーバー グループに適用されます。 |
| リージョン       | Azure Database for PostgreSQL - Hyperscale (Citus) 予約容量の予約の対象となる Azure リージョン。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| 期間         | 1 年間または 3 年間。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Quantity     | Hyperscale (Citus) の予約容量の予約内で購入されるコンピューティング リソース数。 具体的には、予約され、請求時に割引を受ける、選択した Azure リージョンのコーディネーターまたはワーカー ノードの仮想コアの数です。 たとえば、米国東部リージョンで 64 コーディネーター ノードの仮想コアと 32 ワーカー ノードの仮想コアの合計コンピューティング容量を使用して Hyperscale (Citus) サーバー グループを実行している (または実行する予定がある) 場合、すべてのサーバーに対するベネフィットを最大限に活用するために、コーディネーター ノードとワーカー ノードの数量をそれぞれ 64 と 32 に指定します。                                                                                                                                                                                                                                                     |



## <a name="cancel-exchange-or-refund-reservations"></a>予約の取り消し、交換、または返金

一定の制限付きで、予約の取り消し、交換、または返金を行うことができます。 詳しくは、「[Azure の予約のセルフサービスによる交換と払戻](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)」を参照してください。

## <a name="vcore-size-flexibility"></a>仮想コアのサイズの柔軟性

仮想コアのサイズの柔軟性により、予約容量のベネフィットを失うことなく、同じリージョン内のコーディネーターとワーカー ノードをスケールアップまたはスケールダウンできます。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。

## <a name="next-steps"></a>次のステップ

仮想コアの予約割引は、Azure Database for PostgreSQL の予約容量の予約スコープと属性に一致する複数の Hyperscale (Citus) サーバー グループに自動的に適用されます。 Azure Database for PostgreSQL - Hyperscale (Citus) の予約容量の予約スコープは、Azure portal、PowerShell、Azure CLI、または API で更新できます。

Azure の予約の詳細については、次の記事を参照してください。

* [Azure の予約とは](../cost-management-billing/reservations/save-compute-costs-reservations.md)
* [Azure の予約の管理](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Azure の予約割引を理解する](../cost-management-billing/reservations/understand-reservation-charges.md)
* [従量課金制サブスクリプションの予約使用量について](../cost-management-billing/reservations/understand-reservation-charges-postgresql.md)
* [マイクロソフトエンタープライズ契約の予約の使用状況について](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [パートナー センターのクラウド ソリューション プロバイダー プログラムでの Azure の予約](/partner-center/azure-reservations)