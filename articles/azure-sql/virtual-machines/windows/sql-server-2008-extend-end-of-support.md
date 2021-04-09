---
title: SQL Server 2008 と 2008 R2 のサポート延長
description: Azure に SQL Server インスタンスを移行するか、オンプレミスでインスタンスを保持する延長サポートを購入すると、SQL Server 2008 および SQL Server 2008 R2 のサポートが延長されます。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 4c25adc16d14b4a5fb72ae0103ca05b193b40499
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359168"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Azure での SQL Server 2008 および SQL Server 2008 R2 のサポート延長
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

SQL Server 2008 と SQL Server 2008 R2 は両方とも[サポート ライフ サイクルの終了 (EOS)](https://www.microsoft.com/sql-server/sql-server-2008) に達しています。 多くのお客様がまだ両方のバージョンを使用されていることから、引き続きサポートを利用していただくためのいくつかのオプションを提供しています。 オンプレミスの SQL Server インスタンスを Azure 仮想マシン (VM) に移行するか、Azure SQL Database に移行するか、またはオンプレミスのままで延長セキュリティ更新プログラムを購入することができます。

マネージド インスタンスとは異なり、Azure VM への移行にアプリケーションの再認定は必要ありません。 また、オンプレミスに留まる場合とは異なり、Azure VM に移行することで無料の延長セキュリティ パッチを受信します。

この記事の残りの部分では、Azure VM に SQL Server インスタンスを移行するための考慮事項を示します。

サポート終了オプションの詳細については、「[サポートの終了](/sql/sql-server/end-of-support/sql-server-end-of-life-overview)」を参照してください。

## <a name="provisioning"></a>プロビジョニング

Azure Marketplace で、従量課金制の **SQL Server 2008 R2 on Windows Server 2008 R2** イメージが提供されています。

SQL Server 2008 をご使用のお客様は、ご自分でインストールするか、SQL Server 2008 R2 にアップグレードする必要があります。 同様に、Windows Server 2008 をご使用のお客様は、カスタム VHD から VM をデプロイするか、Windows Server 2008 R2 にアップグレードする必要があります。

Azure Marketplace からデプロイされたイメージには、SQL IaaS 拡張機能が事前インストールされています。 SQL IaaS 拡張機能は、柔軟性のあるライセンスと自動化された修正の要件です。 ご自分でインストールした VM をデプロイするお客様は、SQL IaaS 拡張機能を手動でインストールする必要があります。 SQL IaaS 拡張機能は Windows Server 2008 ではサポートされていません。

> [!NOTE]
> SQL Server の **[作成]** ブレードと **[管理]** ブレードでは Azure portal で SQL Server 2008 R2 イメージを操作しますが、次の機能は "_サポートされません_": 自動バックアップ、Azure Key Vault の統合、R Services、およびストレージの構成。

## <a name="licensing"></a>ライセンス
従量課金制の SQL Server 2008 R2 のデプロイは、[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)に変換できます。

ソフトウェア アシュアランス (SA) ベースのライセンスを従量課金制に変換する場合、お客様は [SQL IaaS Agent 拡張機能](sql-agent-extension-manually-register-single-vm.md)に登録する必要があります。 登録後、SQL ライセンスの種類が Azure ハイブリッド特典と従量課金制の間で交換可能になります。

Azure VM にご自分でインストールした SQL Server 2008 または SQL Server 2008 R2 のインスタンスは、SQL IaaS Agent 拡張機能に登録して、ライセンスの種類を従量課金制に変換することができます。

## <a name="migration"></a>移行
バックアップと復元を手動で行う方法で Azure VM に EOS SQL Server インスタンスを移行することができます。 これは、オンプレミスから Azure VM への最も一般的な移行方法です。

### <a name="azure-site-recovery"></a>Azure Site Recovery

一括移行の場合は、[Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) サービスをお勧めします。 Azure Site Recovery を使用すると、お客様は、SQL Server を含む VM 全体をオンプレミスから Azure VM にレプリケートできます。

SQL Server には、回復を保証するために、アプリ整合性のある Azure Site Recovery スナップショットが必要です。 Azure Site Recovery は、最小 1 時間間隔でのアプリ整合性スナップショットをサポートしています。 Azure Site Recovery による SQL Server の移行で達成可能な最小 RPO (回復ポイントの目標) は 1 時間です。 RTO (目標復旧時間) は 2 時間 + SQL Server の復旧時間です。

### <a name="database-migration-service"></a>Database Migration Service

[Azure Database Migration Service](../../../dms/dms-overview.md) は、お客様が SQL Server を 2012 バージョン以降にアップグレードすることでオンプレミスから Azure VM に移行する場合のオプションです。

## <a name="disaster-recovery"></a>障害復旧

Azure VM 上の EOS SQL Server のディザスター リカバリー ソリューションは次のとおりです。

- **SQL Server のバックアップ**:Azure Backup を使用して、15 分の RPO と特定の時点への復旧で、ランサムウェアや偶発的な削除や破損から EOS SQL Server 2008 および 2008 R2 を保護できます。 詳細については、[こちらの記事](../../../backup/sql-support-matrix.md#scenario-support)を参照してください。
- **ログ配布**:継続的な復元によって別のゾーンまたは Azure リージョンにログ配布のレプリカを作成して、RTO を短縮することができます。 ログ配布は、お客様が手動で構成する必要があります。
- **Azure Site Recovery**:Azure Site Recovery のレプリケーションによって、ゾーンとリージョン間で VM をレプリケートできます。 SQL Server には、障害の発生時に回復を保証するために、アプリ整合性スナップショットが必要です。 Azure Site Recovery は、EOS SQL Server のディザスター リカバリーで最小 1 時間の RPO と、2 時間 (+ SQL Server の復旧時間) の RTO を実現します。

## <a name="security-patching"></a>セキュリティ修正

SQL Server VM の延長セキュリティ更新プログラムは、SQL Server VM が [SQL IaaS Agent 拡張機能](sql-agent-extension-manually-register-single-vm.md)に登録された後、Microsoft Update チャンネルを通じて配信されます。 パッチは、手動または自動でダウンロードできます。

*[自動修正]* は、既定で有効になります。 自動修正を有効にすると、Azure は SQL Server とオペレーティング システムに修正プログラムを自動的に適用します。 SQL Server IaaS 拡張機能がインストールされている場合は、メンテナンス期間の曜日、時刻、および期間を指定できます。 Azure は、修正プログラムの適用をこのメンテナンス ウィンドウで実行します。 メンテナンス ウィンドウのスケジュールでは、VM のロケールが時刻に使用されます。 詳細については、[Azure Virtual Machines での SQL Server の自動修正](automated-patching.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Azure への SQL Server VM の移行:

* [Azure VM の SQL Server への SQL Server データベースの移行](migrate-to-vm-from-sql-server.md)

Azure Virtual Machines における SQL Server の概要:

* [Azure Portal での SQL Server VM の作成](sql-vm-create-portal-quickstart.md)

SQL Server VM についてよく寄せられる質問とその回答:

* [Azure Virtual Machines における SQL Server に関する FAQ](frequently-asked-questions-faq.md)

サポートの終了オプションやセキュリティ延長更新の詳細は次にあります。

* [サポートの終了](/sql/sql-server/end-of-support/sql-server-end-of-life-overview) & [セキュリティ延長更新](/sql/sql-server/end-of-support/sql-server-extended-security-updates)