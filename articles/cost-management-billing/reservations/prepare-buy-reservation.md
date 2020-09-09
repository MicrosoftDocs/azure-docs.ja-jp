---
title: Azure の予約の購入
description: Azure の予約を購入するうえで役立つ重要なポイントについて説明します。
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 211e06695d3cabef8cfeb0a7bbf581835819345c
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686209"
---
# <a name="buy-a-reservation"></a>予約の購入

Azure の予約には、多数の Azure リソースに対する計画を 1 年分または 3 年分コミットすることで、コストを削減する効果があります。 予約を購入するというコミットメントを締結する前に、以降の各セクションに目を通して購入の準備をするようにしてください。

## <a name="who-can-buy-a-reservation"></a>だれが予約を購入できるか

プランを購入するには、Enterprise のサブスクリプション所有者ロール (MS-AZR-0017P または MS-AZR-0148P)、従量課金制サブスクリプション (MS-AZR-0003P または MS-AZR-0023P)、Microsoft 顧客契約サブスクリプションのいずれかが必要です。 クラウド ソリューション プロバイダーは、Azure portal または [パートナー センター](/partner-center/azure-reservations) を使用して Azure の予約を購入できます。

Enterprise Agreement (EA) のお客様は、EA Portal 内で、 **[予約インスタンスを追加します]** オプションを無効にして、購入を EA 管理者に限定することができます。 EA 管理者は、予約を購入するために、少なくとも 1 つの EA サブスクリプションのサブスクリプション所有者である必要があります。 このオプションは、さまざまなコスト センター用に予約を購入する集中管理チームを必要とする企業に役立ちます。 購入後、集中管理チームは、予約にコスト センター所有者を追加できます。 これにより、所有者は、そのサブスクリプションに予約のスコープを設定できます。 集中管理チームは、予約が購入されたサブスクリプション所有者のアクセス権を持つ必要はありません。

予約割引が適用されるのは、エンタープライズ、クラウド ソリューション プロバイダー (CSP)、Microsoft 顧客契約、および従量課金制の個人プランを通じて購入したサブスクリプションに関連付けられたリソースのみです。

## <a name="scope-reservations"></a>予約のスコープ設定

予約のスコープをサブスクリプションまたはリソース グループに設定することができます。 予約のスコープを設定すると、予約の割引が適用される場所が選択されます。 予約のスコープをリソース グループに設定すると、予約割引はサブスクリプション全体ではなく、リソース グループにのみ適用されます。

### <a name="reservation-scoping-options"></a>予約スコープのオプション

予約のスコープ設定には、ニーズに応じて次の 3 つのオプションがあります。

- **単一のリソース グループのスコープ** - 選択されたリソース グループ内の一致するリソースにのみ、予約割引を適用します。
- **単一サブスクリプション** - 選択されたサブスクリプションの一致するリソースに予約割引を適用します。
- **共有スコープ** - 課金コンテキスト内にある有効なサブスクリプションの一致するリソースに予約割引を適用します。
    - マイクロソフト エンタープライズ契約のお客様の場合、課金コンテキストは登録です。 予約の共有スコープには、登録内の複数の Active Directory テナントが含まれます。
    - Microsoft 顧客契約のお客様の場合、課金スコープは課金プロファイルです。
    - 従量課金制料金の個々のサブスクリプションの場合、課金スコープはアカウント管理者によって作成されるすべての有効なサブスクリプションです。

使用量に予約割引を適用しつつ、Azure では次の順序で予約が処理されます。

1. リソース グループにスコープが設定されている予約
2. 単一スコープの予約
3. 共有スコープの予約

スコープは、予約の購入後にいつでも更新できます。 これを行うには、予約にアクセスし、 **[構成]** をクリックして予約のスコープを再設定します。 予約のスコープの再設定は、商用トランザクションではありません。 予約期間は変更されません。 スコープの更新の詳細については、[予約購入後にスコープを更新する](manage-reserved-vm-instance.md#change-the-reservation-scope)方法に関するセクションを参照してください。

![予約のスコープの変更を示す例](./media/prepare-buy-reservation/rescope-reservation-resource-group.png)

## <a name="discounted-subscription-and-offer-types"></a>割引サブスクリプションとオファーの種類

予約割引は、次の有効なサブスクリプションとオファーの種類に適用されます。

- マイクロソフト エンタープライズ契約 (オファー番号:MS-AZR-0017P または MS-AZR-0148P)
- Microsoft 顧客契約のサブスクリプション
- 従量課金制の個人プラン (プラン番号:MS-AZR-0003P または MS-AZR-0023P)
- CSP サブスクリプション

他のプランの種類のサブスクリプションで実行されるリソースは、予約割引の対象外です。

## <a name="purchase-reservations"></a>購入予約

予約の購入は、Azure portal、API、PowerShell、CLI から行うことができます。 予約の購入準備が整ったら、次の中から、ご自身に該当する記事をお読みください。

- [App Service](prepay-app-service-isolated-stamp.md)
- [Azure Cache for Redis](../../azure-cache-for-redis/cache-reserved-pricing.md)
- [Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Databricks](prepay-databricks-reserved-capacity.md)
- [Data Explorer](/azure/data-explorer/pricing-reserved-capacity)
- [Disk Storage](../../virtual-machines/disks-reserved-capacity.md)
- [Dedicated Host](../../virtual-machines/prepay-dedicated-hosts-reserved-instances.md)
- [ソフトウェア プラン](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Storage](../../storage/blobs/storage-blob-reserved-capacity.md)
- [SQL Database](../../azure-sql/database/reserved-capacity-overview.md)
- [Azure Database for PostgreSQL](../../postgresql/concept-reserved-pricing.md)
- [Azure Database for MySQL](../../mysql/concept-reserved-pricing.md)
- [Azure Database for MariaDB](../../mariadb/concept-reserved-pricing.md)
- [Azure Synapse Analytics](prepay-sql-data-warehouse-charges.md)
- [仮想マシン](../../virtual-machines/windows/prepay-reserved-vm-instances.md)

## <a name="buy-reservations-with-monthly-payments"></a>月払いで予約を購入する

予約は月払いで購入することができます。 全額を支払う前払いでの購入とは異なり、月払いを選択すると、対象期間の月数で予約の総コストが均等に分割されます。 前払いも月払いも予約の総コストは同じであり、月払いを選んだとしても追加料金を支払う必要はありません。

Microsoft 顧客契約 (MCA) を使用して予約を購入する場合、毎月の支払金額は、現地通貨に対する当月の市場相場によって異なる場合があります。

以下は月払いの対象外です。Databricks、SUSE Linux の予約、Red Hat プラン、Azure Red Hat OpenShift のライセンス。

### <a name="view-payments-made"></a>過去の支払いを確認する

過去の支払いは、API や利用状況データを使用して確認できるほか、コスト分析で確認できます。 月払いで支払われた予約の場合、その頻度の値が **[定期的]** として、利用状況データと Reservation Charges API に表示されます。 前払いで支払われた予約の場合、この値が **[onetime]\(1 回\)** として表示されます。

コスト分析では、月払いの購入が既定のビューに表示されます。 すべての購入を表示するには、 **[Charge type]\(料金タイプ\)** に **[購入]** フィルターを、 **[頻度]** に **[定期的]** を適用します。 予約のみを表示するには、 **[予約]** のフィルターを適用します。

![コスト分析で予約の購入コストを表示する例](./media/prepare-buy-reservation/cost-analysis.png)

### <a name="exchange-and-refunds"></a>交換と払戻

月払いで購入した予約は、他の予約と同様、払戻を受けたり交換したりすることができます。 

月払いで購入した予約を交換する場合、新たな購入対象の総生涯コストが、予約の返上で取り消される支払残金よりも大きくなければなりません。 交換に関しては、それ以外の制限や料金はありません。 前払いで購入した予約と引き換えに、新しい予約を月払いで購入することができます。 ただし、新しい予約の生涯価値は、返上する予約の実際の量に応じた価値よりも大きくなければなりません。

月払いの予約をキャンセルした場合、取り消しの対象となる将来支払い分の上限は、払戻の上限である $50,000 米国ドルとなります。

交換と払戻の詳細については、「[Azure の予約のセルフサービスによる交換と払戻](exchange-and-refund-azure-reservations.md)」を参照してください。

## <a name="reservation-notifications"></a>予約通知

Azure サブスクリプションの支払い方法によっては、組織内の次のユーザーに予約通知がメールで送られます。 次のようなさまざまなイベントに対して通知が送信されます。 

- Purchase
- 今後の予約の有効期限
- Expiry
- 更新
- キャンセル
- スコープの変更

EA サブスクリプションをお持ちのお客様の場合:

- 通知は、EA 通知の連絡先にのみ送信されます。
- RBAC (IAM) アクセス許可を使用して予約に追加されたユーザーは、メール通知を受け取りません。

個々のサブスクリプションをお持ちのお客様の場合:

- 購入者は、購入通知を受け取ります。
- 購入時に、サブスクリプション課金アカウントの所有者が購入通知を受け取ります。
- アカウント所有者は、他のすべての通知を受け取ります。

## <a name="next-steps"></a>次のステップ

- [Azure リソースに対する予約を管理する](manage-reserved-vm-instance.md)
