---
title: Azure 予約割引の適用方法
description: この記事では、予約インスタンスの割引が通常どのように適用されるかについてわかりやすく説明しています。
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: banders
ms.openlocfilehash: 1c038e896a9f9fc65c3b2201b3e763e966285c56
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684679"
---
# <a name="how-a-reservation-discount-is-applied"></a>予約割引の適用方法

この記事では、予約インスタンスの割引が通常どのように適用されるかについてわかりやすく説明しています。 予約割引は、予約購入時に選択した属性と一致するリソースの使用に適用されます。 代表的な属性の 1 つは、一致する VM、SQL Database、Azure Cosmos DB、他のリソースが実行されるスコープです。 たとえば米国西部リージョンの Standard D2 仮想マシンの予約割引を希望する場合、その VM が実行されているサブスクリプションを選択します。

予約割引は、"*使用しないと失われます*"。 ある時間、一致するリソースがない場合は、その時間に対する予約量は失われます。 未使用の予約済み時間を繰り越すことはできません。

リソースをシャットダウンすると、予約割引は、指定されたスコープ内の別の一致するリソースに自動的に適用されます。 指定したスコープ内に一致するリソースが見つからない場合、予約済み時間は "*失われます*"。

たとえば、後でリソースを作成して、使用率が低い一致する予約がある場合があります。 予約割引は、新しい一致するリソースに自動的に適用されます。

その仮想マシンが、加入契約/アカウント内の複数の異なるサブスクリプションで実行される場合は、共有スコープを選択します。 共有スコープによって、サブスクリプションをまたぐ予約割引の適用が可能となります。 スコープは、予約の購入後に変更することができます。 詳しくは、[Azure の予約の管理](manage-reserved-vm-instance.md)に関する記事をご覧ください。

予約割引が適用されるのは、エンタープライズ、Microsoft 顧客契約、CSP、または従量課金制のサブスクリプションに関連付けられたリソースのみです。 他のプランの種類のサブスクリプションで実行されるリソースは、予約割引の対象外です。

## <a name="when-the-reservation-term-expires"></a>予約期間が終了したとき

予約期間が満了した時点で、割引の有効期限は切れ、従量課金料金がリソースに適用されます。 既定では、予約の自動更新は設定されていません。 予約の自動更新を有効にするには、更新設定でオプションを選択します。 自動更新を有効にすると、既存の予約の有効期限が切れたときに置換予約が購入されます。 既定では、置換予約の属性は期限切れになる予約の属性と同じです。更新設定では、必要に応じて請求頻度、期間、または数量を変更することもできます。 予約に対する所有者アクセス権を持つユーザーと、課金に使用されるサブスクリプションは、更新を設定できます。  

## <a name="discount-applies-to-different-sizes"></a>さまざまなサイズに割引が適用される

予約を購入するときに、同じサイズのグループ内にある属性を持つ他のインスタンスに割引を適用できます。 この機能は、インスタンス サイズの柔軟性と呼ばれます。 割引範囲の柔軟性は、予約のタイプと、予約を購入するときに選択する属性によって異なります。

サービス プラン:

- 予約 VM インスタンス:予約を購入し、 **[インスタンス サイズの柔軟性に対する最適化]** を選択すると、割引範囲は選択する仮想マシンのサイズによって決まります。 予約は同じサイズの系列グループの仮想マシン (VM) のサイズに適用できます。 詳細については、「[Reserved VM Instances での仮想マシン サイズの柔軟性](../../virtual-machines/reserved-vm-instance-size-flexibility.md)」を参照してください。
- Azure Storage の予約容量: Azure Storage の標準アカウントの予約容量を、1 か月あたり 100 TiB または 1 PiB 単位で購入できます。 Azure Storage の予約容量をサポートするリージョンについては、「[ブロック BLOB の価格](https://azure.microsoft.com/pricing/details/storage/blobs/)」を参照してください。 Azure Storage の予約容量はすべてのアクセス層 (ホット、クール、アーカイブ) および任意のレプリケーション構成 (LRS、GRS、ZRS) で使用できます。
- SQL Database 予約容量:割引範囲は、選択したパフォーマンス層によって異なります。 詳細については、「[Azure の予約割引の適用方法](understand-reservation-charges.md)」を参照してください。
- Azure Cosmos DB 予約容量:割引範囲は、プロビジョニングされたスループットによって異なります。 詳細については、「[Azure Cosmos DB の予約割引の適用方法](understand-cosmosdb-reservation-charges.md)」を参照してください。

## <a name="how-discounts-apply-to-specific-azure-services"></a>Azure サービスごとの割引の適用方法

各 Azure サービスに対して割引がどのように適用されるかについては、次の記事をお読みください。

- [App Service](reservation-discount-app-service-isolated-stamp.md)
- [Azure Cache for Redis](understand-azure-cache-for-redis-reservation-charges.md)
- [Cosmos DB](understand-cosmosdb-reservation-charges.md)
- [Database for MariaDB](understand-reservation-charges-mariadb.md)
- [Database for MySQL](understand-reservation-charges-mysql.md)
- [Database for PostgreSQL](understand-reservation-charges-postgresql.md)
- [Databricks](reservation-discount-databricks.md)
- [Data Explorer](understand-azure-data-explorer-reservation-charges.md)
- [Dedicated Host](billing-understand-dedicated-hosts-reservation-charges.md)
- [Disk Storage](understand-disk-reservations.md)
- [Red Hat Linux Enterprise](understand-rhel-reservation-charges.md)
- [ソフトウェア プラン](understand-suse-reservation-charges.md)
- [Storage](understand-storage-charges.md)
- [SQL Database](understand-reservation-charges.md)
- [Azure Synapse Analytics](reservation-discount-azure-sql-dw.md)
- [仮想マシン](../manage/understand-vm-reservation-charges.md)


## <a name="next-steps"></a>次のステップ

- [Azure の予約の管理](manage-reserved-vm-instance.md)
- [従量課金制のサブスクリプションの予約使用量について](understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約使用量について](understand-reserved-instance-usage-ea.md)
- [予約に含まれない Windows ソフトウェアのコスト](reserved-instance-windows-software-costs.md)