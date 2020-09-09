---
title: Azure の予約とは
description: 仮想マシン、SQL データベース、Azure Cosmos DB、その他のリソースのコストを節約するための Azure の予約と価格について説明します。
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: overview
ms.date: 08/04/2020
ms.author: banders
ms.openlocfilehash: a5ab489e995506deb806fe5f0cee4a147ae42ac2
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682588"
---
# <a name="what-are-azure-reservations"></a>Azure の予約とは

Azure の予約には、複数の製品に対する計画を 1 年分または 3 年分コミットすることで、コストを削減する効果があります。 コミットすることで、使用するリソースの割り引きを受けることができます。 予約すると、コストを大幅に削減でき、従量課金制の料金を最大 72% 削減できます。 予約は課金割引を提供するもので、リソースの実行時の状態には影響しません。 予約の購入後は、該当するリソースに割引が自動的に適用されます。

予約の支払いは、前払いとすることも、月払いとすることもできます。 前払いも月払いも予約の総コストは同じであり、月払いを選んだとしても追加料金を支払う必要はありません。 毎月の支払いは、サードパーティ製品ではなく、Azure の予約で利用できます。

予約は [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) で購入できます。

## <a name="why-buy-a-reservation"></a>予約を購入する理由

予約をサポートする一定のリソース使用量がある場合は、コスト削減の手段として、予約を購入することができます。 たとえば予約なしでサービスのインスタンスを常時実行した場合、従量課金制の料金が請求されます。 予約を購入すると、予約割引がすぐに得られます。 リソースには従量課金制の料金が適用されなくなります。

## <a name="how-reservation-discount-is-applied"></a>予約割引の適用方法

予約の購入後は、購入時に選択した属性と一致するリソースの使用に自動的に割引が適用されます。 属性の例としては、SKU、リージョン (該当する場合)、スコープなどがあります。 予約の割引が適用される範囲は、予約のスコープによって選択されます。

割引の適用方法について詳しくは、[予約インスタンスの割引の適用](reservation-discount-application.md)に関するページを参照してください。

予約のスコープのしくみについて詳しくは、「[予約のスコープ設定](prepare-buy-reservation.md#scope-reservations)」を参照してください。

## <a name="determine-what-to-purchase"></a>購入内容の決定 

Azure Databricks を除くすべての予約は、1 時間単位で適用されます。 基準となる一定の使用量に基づいて予約を購入することを検討してください。 利用状況データを分析するか、予約の推奨情報を利用して、購入する予約を決めることができます。 推奨情報は次の方法で入手できます。

- Azure Advisor (VM のみ)
- Azure portal の予約購入エクスペリエンス
- Cost Management Power BI アプリ
- API 

詳細については、「 [購入する予約を判断する](determine-reservation-purchase.md)」を参照してください。 

## <a name="buying-a-reservation"></a>予約の購入 

予約の購入は、Azure portal、API、PowerShell、CLI から行うことができます。 

Azure portal にアクセスして購入 (https://ms.portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Docs) 

詳細については、「 [予約の購入](prepare-buy-reservation.md)」を参照してください。 

## <a name="how-is-a-reservation-billed"></a>予約はどのように課金されますか 

予約は、サブスクリプションに関連付けられている支払い方法に対して課金されます。 予約コストは年額コミットメントの残高から差し引かれます (可能な場合)。 年額コミットメントの残高で予約のコストを賄えない場合は、超過分が課金されます。 従量課金制の個人プランのサブスクリプションをご利用の場合、前払い購入では、アカウントに登録されているクレジット カードに直接課金されます。 月支払いの場合は請求書に表示され、クレジット カードに毎月請求されます。 請求書による課金の場合、次回の請求書に料金が表示されます。 

## <a name="permissions-to-view-and-manage-reservations"></a>予約を表示、管理するためのアクセス許可 

予約を購入するユーザーと予約の請求に使用されるサブスクリプションのアカウント管理者には、予約注文と予約とに対する所有者ロールが割り当てられます。

予約注文または予約でのロールにユーザーを追加することで、予約管理を委任することができます。 ロールの割り当ては Azure portal で行うか、API と PowerShell を使用して行います。 

詳細については、「 [予約を管理できるユーザーを追加または変更する](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)」を参照してください。 

## <a name="get-reservation-details-and-utilization-after-purchase"></a>予約の詳細と購入後の使用状況の入手

予約を表示するためのアクセス許可があれば、予約とその使用状況を Azure portal で確認できます。 API を使用してデータを取得することもできます。 

Azure portal で予約を確認する方法の詳細については、 [Azure portal での予約の確認](view-reservations.md)に関するページを参照してください。 

## <a name="manage-reservations-after-purchase"></a>購入後の予約の管理 

Azure の予約を購入した後、予約の適用範囲を別のサブスクリプションに更新したり、予約を管理できるユーザーを変更したりできます。また、予約を細かく分割したり、インスタンス サイズの柔軟性を変更したりすることもできます。 

詳細については、「 [Azure リソースに対する予約を管理する](manage-reserved-vm-instance.md)」を参照してください。 

## <a name="flexibility-with-azure-reservations"></a>Azure の予約が備える柔軟性

Azure の予約は、変化するニーズを満たすために柔軟性を提供します。 変化するニーズを満たすために、予約を同じ種類の別の予約と交換できます。 また、予約が不要になった場合は、12 か月のローリング ウィンドウで最大 $50,000 USD までの払い戻しができます この返金の上限は、お客様の Microsoft との契約の範囲内のすべての予約が対象です。

詳しくは、「[Azure の予約のセルフサービスによる交換と払戻](exchange-and-refund-azure-reservations.md)」を参照してください。


## <a name="charges-covered-by-reservation"></a>予約の対象となる料金

- **予約仮想マシン インスタンス** - 予約の対象は仮想マシンとクラウド サービスのコンピューティング コストのみです。 その他のソフトウェア、Windows、ネットワーク、ストレージの料金は含まれません。
- **Azure Storage の予約容量** - 予約の対象となるのは、BLOB ストレージまたは Azure Data Lake Gen2 ストレージの標準ストレージ アカウントのストレージ容量です。 帯域幅またはトランザクション レートは予約の対象外です。
- **Azure Cosmos DB の予約容量** - 予約は、リソース用にプロビジョニングされたスループットが対象になります。 ストレージとネットワーク料金は対象外です。
- **SQL Database の予約された仮想コア** - 予約にはコンピューティング コストのみが含まれます。 SQL ライセンスの料金は別途請求されます。
- **Azure Synapse Analytics** - 予約には cDWU の使用が含まれます。 Azure Synapse Analytics の使用に関連するストレージまたはネットワークの料金は含まれません。
- **Azure Databricks** - 予約の対象となるのは、DBU の使用のみです。 コンピューティング、ストレージ、およびネットワーキングなどの他の料金は別途適用されます。
- **App Service スタンプ料金** - 予約にはスタンプの使用が含まれます。 worker には適用されないので、スタンプに関連する他のすべてのリソースには別途課金されます。
- **Azure Database for MySQL** - 予約にはコンピューティング コストのみが含まれます。 予約には、MySQL データベース サーバーに関連するソフトウェア、ネットワーク、またはストレージの料金は含まれません。
- **Azure Database for PostgreSQL** - 予約にはコンピューティング コストのみが含まれます。 予約には、PostgreSQL データベース サーバーに関連するソフトウェア、ネットワーク、またはストレージの料金は含まれません。
- **Azure Database for MariaDB** - 予約にはコンピューティング コストのみが含まれます。 予約には、MariaDB データベース サーバーに関連するソフトウェア、ネットワーク、またはストレージの料金は含まれません。
- **Azure Data Explorer** - 予約の対象となるのは、マークアップ料金です。 クラスターに関連付けられているコンピューティング、ネットワーク、またはストレージの料金には、予約は適用されません。
- **Azure Cache for Redis** - 予約にはコンピューティング コストのみが含まれます。 Redis キャッシュ インスタンスに関連付けられているネットワークまたはストレージの料金は、予約の対象になりません。
- **Azure Dedicated Host** - Dedicated Host にはコンピューティング コストのみが含まれます。
- **Azure Disk Storage の予約** - 予約の対象は、P30 サイズ以上の Premium SSD のみです。 他のディスクの種類や P30 よりも小さいサイズは対象外となっています。

ソフトウェア プラン:

- **SUSE Linux** - 予約の対象となるのは、ソフトウェア プランのコストです。 この割引は SUSE 測定にのみ適用され、仮想マシンの使用には適用されません。
- **Red Hat プラン** - 予約の対象となるのは、ソフトウェア プランのコストです。 この割引は Red Hat 測定にのみ適用され、仮想マシンの使用には適用されません。
- **Azure VMware Solution by CloudSimple** - 予約の対象となるのは、VMWare CloudSimple ノードです。 追加のソフトウェア料金は発生します。
- **Azure Red Hat OpenShift** - 予約は Azure インフラストラクチャのコストではなく、OpenShift のコストに適用されます。

Windows 仮想マシンと SQL Database については、ソフトウェアのコストに予約割引が適用されません。 ライセンス コストは、[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)で充当することができます。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート要求を作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

- Azure の予約の詳細については、次の記事を参照してください。
    - [Azure の予約の管理](manage-reserved-vm-instance.md)
    - [従量課金制のサブスクリプションの予約使用量について](understand-reserved-instance-usage.md)
    - [エンタープライズ加入契約の予約使用量について](understand-reserved-instance-usage-ea.md)
    - [予約に含まれない Windows ソフトウェアのコスト](reserved-instance-windows-software-costs.md)
    - [パートナー センターのクラウド ソリューション プロバイダー (CSP) プログラムでの Azure の予約](/partner-center/azure-reservations)

- サービス プランの予約の詳細については、次の記事を参照してください。
    - [Azure Reserved VM Instances による仮想マシン](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Azure Cosmos DB の容量が予約された Azure Cosmos DB リソース](../../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Azure SQL Database の予約容量を使用した SQL Database 計算リソース](../../azure-sql/database/reserved-capacity-overview.md)
    - [ の予約容量を使用した Azure Cache for Redis リソース](../../azure-cache-for-redis/cache-reserved-pricing.md) ソフトウェア プランの予約の詳細については、次の記事を参照してください。
    - [Azure の予約からの Red Hat ソフトウェア プラン](../../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Azure の予約からの SUSE ソフトウェア プラン](../../virtual-machines/linux/prepay-suse-software-charges.md)
