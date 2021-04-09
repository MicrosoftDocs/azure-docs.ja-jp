---
title: メンテナンス期間
description: Azure SQL Database とマネージド インスタンスのメンテナンス期間を構成する方法について説明します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.custom: references_regions
ms.date: 03/11/2021
ms.openlocfilehash: bd91c29ca97c2096c4d8f3df19dbb9eab306b8e7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103149751"
---
# <a name="maintenance-window-preview"></a>メンテナンス期間 (プレビュー)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

メンテナンス期間機能を使用すると、[Azure SQL Database](sql-database-paas-overview.md) と [Azure SQL マネージド インスタンス](../managed-instance/sql-managed-instance-paas-overview.md) リソースのメンテナンス スケジュールを構成して、影響のあるメンテナンス イベントを予測可能にし、ワークロードの中断を減らすことができます。 

> [!Note]
> メンテナンス期間機能を使用しても、短時間の接続中断を引き起こす可能性のあるハードウェア障害などの計画外のイベントからは保護されません。

## <a name="overview"></a>概要

Azure では、SQL Database と SQL マネージド インスタンス リソースの[計画メンテナンス](planned-maintenance.md)を定期的に実行します。 Azure SQL メンテナンス イベントの間、データベースは完全に利用可能ですが、場合によってはリソースの再構成が必要になるため、[SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database) と [SQL マネージド インスタンス](https://azure.microsoft.com/support/legal/sla/azure-sql-sql-managed-instance)のそれぞれの可用性 SLA 内で短時間のフェールオーバーが発生する可能性があります。

メンテナンス期間は、データベースまたはインスタンスのフェールオーバーに対して回復性がなく、計画メンテナンス イベントによる短時間の接続中断を許容できない運用環境のワークロードを対象としています。 希望するメンテナンス期間を選択することで、計画メンテナンスの影響を最小限に抑えることができます。それが営業時間のピーク時以外に行われるためです。 回復性があるワークロードと非運用環境ワークロードは、Azure SQL の既定のメンテナンス ポリシーに依存できます。

メンテナンス期間は、作成時に構成することも、既存の Azure SQL リソースに対して構成することもできます。 これは、Azure portal、PowerShell、CLI、または Azure API を使用して構成できます。

> [!Important]
> メンテナンス期間の構成は、Azure SQL リソースのサービス レベルの変更と同様に、時間のかかる非同期操作です。 操作の終了時に発生する短いフェールオーバーを除いて、操作中にリソースを使用できます。これは、長時間のトランザクションが中断された場合でも、通常は最大 8 秒です。 フェールオーバーの影響を最小限に抑えるには、ピーク時以外に操作を実行する必要があります。

### <a name="gain-more-predictability-with-maintenance-window"></a>メンテナンス期間の予測可能性を高める

既定では、Azure SQL のメンテナンス ポリシーは、一般的な営業時間のピーク時の中断を回避するために、**毎日現地時刻で午前 8 時から午後 5 時まで** の間、影響のある更新をブロックします。 現地時刻は、リソースをホストする [Azure リージョン](https://azure.microsoft.com/global-infrastructure/geographies/)の場所によって決定され、ローカル タイム ゾーンの定義に従って夏時間が適用される場合があります。 

次の 2 つの追加メンテナンス期間スロットから選択することにより、メンテナンスの更新を Azure SQL リソースに適した時間に調整できます。
 
* 平日期間、月曜日から木曜日の現地時刻で午後 10 時から午前 6 時
* 週末期間、金曜日から日曜日の現地時刻で午後 10 時から午前 6 時

メンテナンス期間を選択し、サービスの構成が完了すると、計画メンテナンスは、選択した期間中のみ行われます。   

> [!Important]
> 重要なセキュリティ パッチの適用など、アクションの延期によって重大な影響が生じる可能性がある非常にまれな状況では、構成されたメンテナンス期間が一時的にオーバーライドされることがあります。 

### <a name="cost-and-eligibility"></a>コストと資格

メンテナンス期間の構成と使用は、対象となるすべての[プランの種類](https://azure.microsoft.com/support/legal/offer-details/) (従量課金制、クラウド ソリューション プロバイダー (CSP)、Microsoft Enterprise Agreement、または Microsoft 顧客契約) で無料です。

> [!Note]
> Azure オファーとは、ご利用の Azure サブスクリプションの種類です。 たとえば、[従量課金制料金](https://azure.microsoft.com/offers/ms-azr-0003p/)、[Azure イン オープン プラン](https://azure.microsoft.com/offers/ms-azr-0111p/)、および [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) のサブスクリプションはすべて Azure プランです。 オファーまたはプランごとに、使用条件と利点は異なります。 オファーまたはプランは、サブスクリプションの概要に表示されます。 サブスクリプションを別のオファーに変更する方法について詳しくは、「[Azure サブスクリプションを別のオファーに変更する](/azure/cost-management-billing/manage/switch-azure-offer)」を参照してください。

## <a name="advance-notifications"></a>事前通知

メンテナンス通知は、Azure SQL Database の次回の計画メンテナンス イベントの 24 時間前に、メンテナンス時に、およびメンテナンスの終了時に通知するように構成できます。 詳しくは、「[事前通知](advance-notifications.md)」をご覧ください。

## <a name="availability"></a>可用性

### <a name="supported-service-level-objectives"></a>サポートされるサービス レベル目標

既定以外のメンテナンス期間は、すべての SLO で選択できますが、**次の場合を除きます**。
* ハイパースケール 
* インスタンス プール
* レガシ Gen4 vCore
* Basic、S0、および S1 
* DC、Fsv2、M シリーズ

### <a name="azure-region-support"></a>Azure リージョンへの対応

既定以外のメンテナンス期間は、現在、次のリージョンで選択できます。

- オーストラリア東部
- オーストラリア南東部
- ブラジル南部
- カナダ中部
- 米国中部
- 米国東部
- 米国東部 2
- 東アジア
- 東日本
- 米国中北部
- 北ヨーロッパ
- 米国中南部
- 東南アジア
- 英国南部
- 西ヨーロッパ
- 米国西部
- 米国西部 2

## <a name="gateway-maintenance-for-azure-sql-database"></a>Azure SQL Database のゲートウェイ メンテナンス

メンテナンス期間の最大のメリットを得るには、クライアント アプリケーションでリダイレクト接続ポリシーを使用していることが必要です。 リダイレクトは推奨の接続ポリシーであり、このときクライアントはデータベースをホストしているノードへの直接接続を確立します。これにより、待機時間が短縮され、スループットが向上します。  

* Azure SQL Database では、プロキシ接続ポリシーを使用した接続は、選択したメンテナンス期間とゲートウェイ ノードのメンテナンス期間の両方の影響を受ける可能性があります。 ただし、推奨のリダイレクト接続ポリシーを使用したクライアント接続であれば、ゲートウェイ ノードのメンテナンス フェールオーバーの影響を受けません。 

* Azure SQL マネージド インスタンスでは、ゲートウェイ ノードは[仮想クラスター内](../../azure-sql/managed-instance/connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)でホストされ、マネージド インスタンスと同じメンテナンス期間を使用しますが、メンテナンス イベント中の中断の回数を最小限に抑えるために、リダイレクト接続ポリシーを使用することをお勧めします。

Azure SQL Database でのクライアント接続ポリシーについて詳しくは、[Azure SQL Database の接続ポリシー](../database/connectivity-architecture.md#connection-policy)に関するページをご覧ください。 

Azure SQL マネージド インスタンスでのクライアント接続ポリシーについて詳しくは、[Azure SQL マネージド インスタンスの接続の種類](../../azure-sql/managed-instance/connection-types-overview.md)に関するページをご覧ください。

## <a name="considerations-for-azure-sql-managed-instance"></a>Azure SQL マネージド インスタンスに関する考慮事項

Azure SQL マネージド インスタンスは、お客様の仮想ネットワーク サブネット内で実行される、隔離された一連の専用仮想マシンでホストされたサービス コンポーネントで構成されます。 これらの仮想マシンは、複数のマネージド インスタンスをホストできる[仮想クラスター](/azure/azure-sql/managed-instance/connectivity-architecture-overview#high-level-connectivity-architecture)を形成します。 1 つのサブネットのインスタンスに対して構成されたメンテナンス期間は、サブネット内の仮想クラスターの数と、仮想クラスター間でのインスタンスの分散に影響を与える可能性があります。 これには、いくつかの影響を考慮する必要がある場合があります。

### <a name="maintenance-window-configuration-is-long-running-operation"></a>メンテナンス期間の構成は実行時間の長い操作です 
仮想クラスターでホストされているすべてのインスタンスは、メンテナンス期間を共有します。 既定では、すべてのマネージド インスタンスは、既定のメンテナンス期間を使用して仮想クラスターでホストされます。 その作成時または作成後にマネージド インスタンスに対して別のメンテナンス期間を指定すると、それは、対応するメンテナンス期間を持つ仮想クラスターに配置する必要があります。 そのような仮想クラスターがサブネット内に存在しない場合は、そのインスタンスを格納するために、最初に新しいものを作成する必要があります。 既存の仮想クラスターに追加のインスタンスを格納するには、クラスターのサイズ変更が必要になる場合があります。 どちらの操作も、マネージド インスタンスのメンテナンス期間の構成にかかる時間に影響します。
マネージド インスタンスでのメンテナンス期間の構成に予想される時間は、[インスタンス管理操作の予想時間](/azure/azure-sql/managed-instance/management-operations-overview#duration)を使用して計算できます。

> [!Important]
> メンテナンス操作の終了時に短いフェールオーバーが発生します。これは、実行時間の長いトランザクションが中断された場合でも、通常は最大で 8 秒です。 フェールオーバーの影響を最小限に抑えるには、ピーク時以外に操作をスケジュールする必要があります。

### <a name="ip-address-space-requirements"></a>IP アドレス空間の要件
サブネット内の新しい各仮想クラスターには、[仮想クラスターの IP アドレスの割り当て](/azure/azure-sql/managed-instance/vnet-subnet-determine-size#determine-subnet-size)に従って、追加の IP アドレスが必要です。 既存のマネージド インスタンスのメンテナンス期間を変更する場合も、対応するサービス レベルの仮想コアのスケーリング シナリオと同様に、[一時的な追加の IP 容量](/azure/azure-sql/managed-instance/vnet-subnet-determine-size#address-requirements-for-update-scenarios)が必要です。

### <a name="ip-address-change"></a>IP アドレスの変更
メンテナンス期間の構成および変更を行うと、サブネットの IP アドレス範囲内で、インスタンスの IP アドレスが変更されます。

> [!Important]
>  IP アドレスを変更した後、NSG とファイアウォール規則によってデータ トラフィックがブロックされないようにしてください。 

## <a name="next-steps"></a>次のステップ

* [事前通知](advance-notifications.md)
* [メンテナンス期間の構成](maintenance-window-configure.md)

## <a name="learn-more"></a>詳細情報

* [メンテナンス期間に関する FAQ](maintenance-window-faq.yml)
* [Azure SQL Database](sql-database-paas-overview.md) 
* [SQL マネージド インスタンス](../managed-instance/sql-managed-instance-paas-overview.md)
* [Azure SQL Database および Azure SQL マネージド インスタンスでの Azure メンテナンス イベントの計画](planned-maintenance.md)





