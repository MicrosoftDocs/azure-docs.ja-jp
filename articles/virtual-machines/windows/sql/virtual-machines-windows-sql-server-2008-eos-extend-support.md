---
title: Azure での SQL Server 2008 および SQL Server 2008 R2 のサポート延長
description: Azure への SQL Server インスタンスの移行、またはオンプレミスでインスタンスを保持する延長サポートの購入によって、SQL Server 2008 および SQL Server 2008 R2 のサポートを延長する方法について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 93e0032cd283eda034519ca29a0e1cf501b5cde6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100468"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Azure での SQL Server 2008 および SQL Server 2008 R2 のサポート延長

SQL Server 2008 および SQL Server 2008 R2 は、両方とも[サポート ライフ サイクルの終了 (EOS)](https://www.microsoft.com/sql-server/sql-server-2008) が近づいています。 多くのお客様がまだ両方のバージョンを使用されていることから、引き続きサポートを利用していただくためのいくつかのオプションを提供しています。 オンプレミスの SQL Server インスタンスを Azure 仮想マシン (VM) に移行するか、Azure SQL Database に移行するか、またはオンプレミスのままで延長セキュリティ更新プログラムを購入することができます。

マネージド インスタンスとは異なり、Azure VM への移行にアプリケーションの再認定は必要ありません。 また、オンプレミスに留まる場合とは異なり、Azure VM に移行することで無料の延長セキュリティ パッチを受信します。

この記事の残りの部分では、Azure VM に SQL Server インスタンスを移行するための考慮事項を示します。

## <a name="provisioning"></a>プロビジョニング

Azure Marketplace で、従量課金制の **SQL Server 2008 R2 on Windows Server 2008 R2** イメージが提供されています。

SQL Server 2008 をご使用のお客様は、ご自分でインストールするか、SQL Server 2008 R2 にアップグレードする必要があります。 同様に、Windows Server 2008 をご使用のお客様は、カスタム VHD から VM をデプロイするか、Windows Server 2008 R2 にアップグレードする必要があります。

Azure Marketplace からデプロイされたイメージには、SQL IaaS 拡張機能が事前インストールされています。 SQL IaaS 拡張機能は、柔軟性のあるライセンスと自動化された修正の要件です。 ご自分でインストールした VM をデプロイするお客様は、SQL IaaS 拡張機能を手動でインストールする必要があります。 SQL IaaS 拡張機能は Windows Server 2008 ではサポートされていません。

> [!NOTE]
> SQL Server の **[作成]** ブレードと **[管理]** ブレードでは Azure portal で SQL Server 2008 R2 イメージを操作しますが、次の機能は "_サポートされません_": 自動バックアップ、Azure Key Vault の統合、R Services、およびストレージの構成。

## <a name="licensing"></a>ライセンス
従量課金制の SQL Server 2008 R2 のデプロイは、[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)に変換できます。

ソフトウェア アシュアランス (SA) ベースのライセンスを従量課金制に変換する場合、お客様は SQL VM [リソースプロバイダー](virtual-machines-windows-sql-register-with-resource-provider.md)に登録する必要があります。 登録後、SQL ライセンスの種類が Azure ハイブリッド特典と従量課金制の間で交換可能になります。

Azure VM にご自分でインストールした SQL Server 2008 または SQL Server 2008 R2 のインスタンスは、SQL VM リソースプロバイダーに登録して、ライセンスの種類を従量課金制に変換することができます。

## <a name="migration"></a>移行
バックアップと復元を手動で行う方法で Azure VM に EOS SQL Server インスタンスを移行することができます。 これは、オンプレミスから Azure VM への最も一般的な移行方法です。

### <a name="azure-site-recovery"></a>Azure Site Recovery

一括移行の場合は、[Azure Site Recovery](/azure/site-recovery/site-recovery-overview) サービスをお勧めします。 Azure Site Recovery を使用すると、お客様は、SQL Server を含む VM 全体をオンプレミスから Azure VM にレプリケートできます。

SQL Server には、回復を保証するために、アプリ整合性のある Azure Site Recovery スナップショットが必要です。 Azure Site Recovery は、最小 1 時間間隔でのアプリ整合性スナップショットをサポートしています。 Azure Site Recovery による SQL Server の移行で達成可能な最小 RPO (回復ポイントの目標) は 1 時間です。 RTO (目標復旧時間) は 2 時間 + SQL Server の復旧時間です。

### <a name="database-migration-service"></a>Database Migration Service

[Database Migration Service](/azure/dms/dms-overview) は、お客様が SQL Server を 2012 バージョン以降にアップグレードすることでオンプレミスから Azure VM に移行する場合のオプションです。

## <a name="disaster-recovery"></a>障害復旧

Azure VM 上の EOS SQL Server のディザスター リカバリー ソリューションは次のとおりです。

- **SQL Server のバックアップ**:Azure Backup を使用して、ランサムウェアや偶発的な削除や破損から EOS SQL サーバーを保護できます。 このソリューションは、現在、EOS SQL Server に対するプレビュー段階であり、Windows 2008 R2 SP1 上で実行される SQL Server 2008 と 2008 R2 がサポートされます。 詳細については、[こちらの記事](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#support-for-sql-server-2008-and-sql-server-2008-r2)を参照してください。
- **ログ配布**:継続的な復元によって別のゾーンまたは Azure リージョンにログ配布のレプリカを作成して、RTO を短縮することができます。 ログ配布は、お客様が手動で構成する必要があります。
- **Azure Site Recovery**:Azure Site Recovery のレプリケーションによって、ゾーンとリージョン間で VM をレプリケートできます。 SQL Server には、障害の発生時に回復を保証するために、アプリ整合性スナップショットが必要です。 Azure Site Recovery は、EOS SQL Server のディザスター リカバリーで最小 1 時間の RPO と、2 時間 (+ SQL Server の復旧時間) の RTO を実現します。

## <a name="security-patching"></a>セキュリティ修正
SQL Server VM の延長セキュリティ更新プログラムは、SQL Server VM が SQL VM [リソースプロバイダー](virtual-machines-windows-sql-register-with-resource-provider.md)に登録された後、Microsoft Update チャンネルを通じて配信されます。 パッチは、手動または自動でダウンロードできます。

*[自動修正]* は、既定で有効になります。 自動修正を有効にすると、Azure は SQL Server とオペレーティング システムに修正プログラムを自動的に適用します。 SQL Server IaaS 拡張機能がインストールされている場合は、メンテナンス期間の曜日、時刻、および期間を指定できます。 Azure は、修正プログラムの適用をこのメンテナンス ウィンドウで実行します。 メンテナンス ウィンドウのスケジュールでは、VM のロケールが時刻に使用されます。  詳細については、[Azure Virtual Machines での SQL Server の自動修正](virtual-machines-windows-sql-automated-patching.md)に関するページを参照してください。


## <a name="next-steps"></a>次の手順

Azure への SQL Server VM の移行:

* [Azure VM の SQL Server への SQL Server データベースの移行](virtual-machines-windows-migrate-sql.md)

Azure Virtual Machines における SQL Server の概要:

* [Azure Portal での SQL Server VM の作成](quickstart-sql-vm-create-portal.md)

SQL Server VM についてよく寄せられる質問とその回答:

* [Azure Virtual Machines における SQL Server に関する FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
