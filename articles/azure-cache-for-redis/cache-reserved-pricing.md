---
title: 予約容量を使用したコンピューティングの前払い - Azure Cache for Redis
description: 予約容量を使用した計算リソースの Azure Cache for RedisRedis を使用した前払い
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 2f3472aa495042749410bc0b9635f0924a02e1fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98598551"
---
# <a name="prepay-for-azure-cache-for-redis-compute-resources-with-reserved-capacity"></a>予約容量を使用した計算リソースの Azure Cache for RedisRedis を使用した前払い

Azure Cache for Redis は、従量課金制の料金と比較して、コンピューティング リソースを事前に支払ってコストを節約するのに役立ちます。 予約容量用の Azure Cache for Redis では、1 年分または 3 年分をキャッシュで前払いすることで、計算コストを大幅に節約できます。 予約容量用にAzure Cache for Redis を購入するには、Azure リージョン、サービス レベル、用語を指定する必要があります。

特定の Azure Cache for Redis インスタンスに予約を割り当てる必要はありません。 既に稼働している Azure Cache for Redis または新たにデプロイされた Azure Cache for Redis では、予約済みのキャッシュ サイズまでの予約価格が自動的に得られます。 予約を購入すると、計算コストを 1 年間または 3 年間分前払いすることになります。 予約を購入すると、予約属性に一致する Azure Cache for Redis の計算料金は従量課金制で請求されなくなります。 キャッシュに関連付けられているネットワーキング、またはストレージの料金は、予約の対象になりません。 予約期間が満了した時点で、課金特典の有効期限は切れ、従量課金料金が Azure Cache for Redis に適用されます。 予約は自動更新されません。 価格の詳細については、[Azure Cache for Redis の予約容量オファー](https://azure.microsoft.com/pricing/details/cache)に関するページを参照してください。

Azure Cache for Redis の予約容量は [Azure portal](https://portal.azure.com/) で購入できます。 予約容量を購入するには:

* 少なくとも 1 つのエンタープライズ サブスクリプションまたは従量課金制料金の個々のサブスクリプションで所有者ロールである必要があります。
* Enterprise サブスクリプションの場合、[EA ポータル](https://ea.azure.com/)で **[予約インスタンスを追加します]** を有効にする必要があります。 または、その設定が無効になっている場合は、ユーザーはサブスクリプションの EA 管理者である必要があります。
* クラウド ソリューション プロバイダー (CSP) プログラムの場合、管理者エージェントまたはセールス エージェントのみが Azure Cache for Redis の予約容量を購入できます。

エンタープライズおよび従量課金制のお客様に対する予約購入の課金方法の詳細については、「[エンタープライズ加入契約に適用される Azure の予約の使用状況について](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)」および「[従量課金制サブスクリプションに適用される Azure の予約の使用状況について](../cost-management-billing/reservations/understand-reserved-instance-usage.md)」をご覧ください。


## <a name="determine-the-right-cache-size-before-purchase"></a>購入する前に適切なキャッシュのサイズを決定する

予約のサイズは、既存のまたはすぐにデプロイされる予定のキャッシュ (特定のリージョン内で同じサービス レベルを使用するもの) で使用されるメモリ サイズの合計に基づいて決める必要があります。

たとえば、2 つのキャッシュを実行しているとします。13 GB で 1 つ、26 GB で 1 つです。 いずれも少なくとも 1 年は必要です。 さらに、季節需要に合わせるため、月あたり既存の 13 GB キャッシュを 26 GB にスケーリングし、その後、元に戻す計画を立てているとします。 この場合は、1 年予約で 1 P2 キャッシュと 1 P3 キャッシュまたは 3 P2 キャッシュを購入することで、最大限に節約できます。 キャッシュ間での配分に関係なく、予約したキャッシュ メモリの合計量に対して割引を受けられます。


## <a name="buy-azure-cache-for-redis-reserved-capacity"></a>予約容量用に Azure Cache for Redis を購入する

予約 VM インスタンスは [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/) から購入できます。 予約の支払いは、[前払いまたは月払い](../cost-management-billing/reservations/prepare-buy-reservation.md)で行います。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **[すべてのサービス]**  >  **[予約]** を選択します。
3. **[追加]** を選択し、[購入予約] ペインで **[Azure Cache for Redis]** を選択してキャッシュの新しい予約を購入します。
4. 必須フィールドに入力します。 選択した属性と一致する既存または新規のデータベースが、予約容量の割引を受けられます。 割引を受ける Azure Cache for Redis インスタンスの実際の数は、選択したスコープと数量によって変わります。


![予約価格の概要](media/cache-reserved-pricing/cache-reserved-price.png)


次の表で、必須フィールドについて説明します。

| フィールド | 説明 |
| :------------ | :------- |
| サブスクリプション   | Azure Cache for Redis の予約容量の予約の支払いに使用するサブスクリプション。 サブスクリプションの支払方法に対して、Azure Cache for Redis の予約容量の予約の前払いコストが課金されます。 サブスクリプションの種類は、マイクロソフト エンタープライズ契約 (プラン番号:MS-AZR-0017P または MS-AZR-0148P) または従量課金制料金の個々の契約 (プラン番号:MS-AZR-0003P または MS-AZR-0023P)。 エンタープライズ サブスクリプションの場合、登録の Azure 前払い (旧称: 年額コミットメント) の残高から料金が差し引かれるか、超過分として課金されます。 従量課金制料金の個々のサブスクリプションの場合、クレジット カードまたはサブスクリプションの請求書に記載されている支払方法に料金が課金されます。
| Scope | 1 つのサブスクリプションまたは複数のサブスクリプション (共有スコープ) を予約のスコープにすることができます。 以下を選択した場合: </br></br> **共有** – 予約割引は、課金のコンテキスト内にある任意のサブスクリプションで実行されている Azure Cache for Redis インスタンスに適用されます。 エンタープライズのお客様の場合、共有スコープが対象の登録であり、登録内のすべてのサブスクリプションが含まれます。 従量課金制のお客様の場合、共有スコープは、アカウント管理者が作成するすべての従量課金制サブスクリプションです。</br></br> **単一サブスクリプション** - 予約割引は選択したサブスクリプションの Azure Cache for Redis インスタンスに適用されます。 </br></br> **[1 つのリソース グループ]** - 予約割引は、選択したサブスクリプションおよびそのサブスクリプション内の選択したリソース グループ内の Azure Cache for Redis インスタンスに適用されます。
| リージョン | Azure Cache for Redis 予約容量の予約の対象となる Azure リージョン。
| Pricing tier | Azure Cache for Redis サーバーのサービス レベル。
| 期間 | 1 年間または 3 年間
| Quantity | Azure Cache for Redis の予約容量の予約内で購入される計算リソース数。 この数量は、予約し、請求時に割り引きを受ける、選択された Azure リージョンとパフォーマンス レベルに含まれるキャッシュの数です。 たとえば、米国東部リージョンで、合計キャッシュ容量を 26 GB とする Azure Cache for Redis サーバーを実行している、または実行する予定の場合、すべてのキャッシュを最大限に活用するため、同等の 26 GB になるような量を指定します。 そうなるのは、1 P3 キャッシュまたは 2 P2 キャッシュです。

## <a name="cancel-exchange-or-refund-reservations"></a>予約の取り消し、交換、または返金

一定の制限付きで、予約の取り消し、交換、または返金を行うことができます。 詳しくは、「[Azure の予約のセルフサービスによる交換と払戻](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)」を参照してください。

## <a name="cache-size-flexibility"></a>キャッシュのサイズの柔軟性

同じパフォーマンス レベルとリージョン内であれば、予約容量のベネフィットを失うことなく、キャッシュのサイズを柔軟にスケールアップまたはスケールダウンできます。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。

## <a name="next-steps"></a>次のステップ

予約割引は、予約スコープと属性に一致する Azure Cache for Redis インスタンスに対して自動的に適用されます。 予約のスコープは、Azure portal、PowerShell、Azure CLI、または API で更新できます。

*  Azure Cache for Redis に予約容量割引が適用される方法については、「[Azure の予約割引の概要](../cost-management-billing/reservations/understand-azure-cache-for-redis-reservation-charges.md)」をご覧ください。

* Azure の予約の詳細については、次の記事を参照してください。

    * [Azure の予約とは](../cost-management-billing/reservations/save-compute-costs-reservations.md)
    * [Azure の予約の管理](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
    * [Azure の予約割引を理解する](../cost-management-billing/reservations/understand-reservation-charges.md)
    * [従量課金制サブスクリプションの予約使用量について](../cost-management-billing/reservations/understand-reservation-charges-mysql.md)
    * [エンタープライズ加入契約の予約使用量について](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    * [パートナー センターのクラウド ソリューション プロバイダー (CSP) プログラムでの Azure の予約](/partner-center/azure-reservations)