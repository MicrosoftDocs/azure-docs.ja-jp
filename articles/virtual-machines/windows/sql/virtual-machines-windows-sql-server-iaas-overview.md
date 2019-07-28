---
title: Azure Windows 仮想マシンでの SQL Server の概要 | Microsoft Docs
description: Azure Virtual Machines で各 SQL Server エディションを実行する方法について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: mashamsft
manager: craigg
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 99c4f0f99af61196cf1a12f2f68a7d10d8b2e6c7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "61477163"
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>Azure Virtual Machines 上の SQL Server とは何か (Windows)

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)

[Azure Virtual Machines 上の SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) では、オンプレミスのハードウェアを一切管理することなく全バージョンの SQL Server をクラウドで利用することができます。 また、SQL Server VM によって従量課金制のライセンス料が単純化されます。

Azure 仮想マシンは、世界中のさまざまな[リージョン](https://azure.microsoft.com/regions/)で実行されます。 また、[マシンのサイズ](../sizes.md)も各種用意されています。 適切なバージョン、エディション、オペレーティング システムの SQL Server VM を仮想マシン イメージ ギャラリーで作成することができます。 さまざまな SQL Server ワークロードに合った最適な仮想マシンを選ぶことができます。

## <a name="automated-updates"></a>自動化された更新

SQL Server Azure VM では、[自動修正](virtual-machines-windows-sql-automated-patching.md)を使用して、Windows と SQL Server の重要な更新プログラムを自動的にインストールするメンテナンス期間をスケジュール設定できます。

## <a name="automated-backups"></a>自動バックアップ

SQL Server Azure VM では[自動バックアップ](virtual-machines-windows-sql-automated-backup-v2.md)を活用できます。これにより、データベースのバックアップが Blob Storage に定期的に作成されます。 また、この手法は手動で使用できます。 詳細については、「[Azure Storage を使用した SQL Server のバックアップと復元](virtual-machines-windows-use-storage-sql-server-backup-restore.md)」を参照してください。

## <a name="high-availability"></a>高可用性

高可用性が必要な場合は、SQL Server 高可用性グループの構成を検討してください。 そのために、1 つの仮想ネットワークで複数の SQL Server Azure VM を使用します。 高可用性ソリューションは手動で構成できるほか、Azure Portal からテンプレートを使用して自動で構成することもできます。 高可用性を実現する各種方法の概要については、「[Azure 仮想マシンにおける SQL Server の高可用性とディザスター リカバリー](virtual-machines-windows-sql-high-availability-dr.md)」を参照してください。

## <a name="performance"></a>パフォーマンス

Azure 仮想マシンは、さまざまなワークロードのニーズを満たす複数のマシンのサイズを提供します。 SQL VM は、パフォーマンス要件に合わせて最適化される自動化されたストレージの構成も提供します。 SQL VM のストレージの構成の詳細については、「[SQL Server VM のストレージの構成](virtual-machines-windows-sql-server-storage-configuration.md)」を参照してください。 パフォーマンスを微調整するには、「[Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス](virtual-machines-windows-sql-performance.md)」を参照してください。

## <a name="get-started-with-sql-vms"></a>SQL VM を使ってみる

まず、必要なバージョン、エディション、オペレーティング システムの SQL Server 仮想マシン イメージを選んでください。 以下のセクションでは、SQL Server 仮想マシン ギャラリー イメージに関する、Azure Portal へのリンクを記載しています。

> [!TIP]
> SQL イメージの料金について詳しくは、「[SQL Server Azure VM の料金ガイダンス](virtual-machines-windows-sql-server-pricing-guidance.md)」をご覧ください。 

### <a id="payasyougo"></a> 従量課金制
次の表に示したのは、従量課金制で提供されている SQL Server イメージの一覧です。

| バージョン | オペレーティング システム | エディション |
| --- | --- | --- |
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)、[Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016)、[Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2EnterpriseWindowsServer2016)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2StandardWindowsServer2016)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2WebWindowsServer2016)、[Express](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2ExpressWindowsServer2016)、[Developer](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4EnterpriseWindowsServer2012R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4StandardWindowsServer2012R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4WebWindowsServer2012R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2)、[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

利用できる Linux SQL Server 仮想マシン イメージを確認するには、「[Azure 仮想マシン (Linux) における SQL Server の概要](../../linux/sql/sql-server-linux-virtual-machines-overview.md)」を参照してください。

> [!NOTE]
> SQL Server VM のライセンス モデルを従量課金制のライセンスから自分の所有ライセンスに変更できるようになりました。 詳細については、[SQL VM のライセンス モデルを変更する方法](virtual-machines-windows-sql-ahb.md)に関するページを参照してください。 

### <a id="BYOL"></a> ライセンス持ち込み
ライセンス持ち込み (BYOL) を利用することもできます。 このシナリオでは、SQL Server ライセンスに対する追加料金はなく、VM のみに対して料金を支払います。  自分のライセンスを持ち込むと、継続的な運用環境のワークロードの費用を時間と共に削減できます。 この方法を利用するための要件については、「[SQL Server Azure VM の料金ガイダンス](virtual-machines-windows-sql-server-pricing-guidance.md#byol)」を参照してください。

ライセンス持ち込みには、既存の従量課金制の SQL VM を変換する方法と、 **{BYOL}** というプレフィックスでイメージをデプロイする方法とがあります。 従量制課金と BYOL との間でライセンス モデルを切り替える方法の詳細については、[SQL VM のライセンス モデルを変更する方法](virtual-machines-windows-sql-ahb.md)に関するページを参照してください。 

| バージョン | オペレーティング システム | エディション |
| --- | --- | --- |
| **SQL Server 2017** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2EnterpriseWindowsServer2016)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4EnterpriseWindowsServer2012R2)、[Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4StandardWindowsServer2012R2) |

Azure portal で利用できない SQL Server の古いイメージを、PowerShell を使用してデプロイすることはできますか。 Powershell を使用して、使用可能なすべてのイメージを表示するには、次のコマンドを使用します。

  ```powershell
  Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
  ```

PowerShell を使用して SQL Server VM をデプロイする方法の詳細については、「[Azure PowerShell を使用して SQL Server 仮想マシンをプロビジョニングする方法](virtual-machines-windows-ps-sql-create.md)」を参照してください。


### <a name="connect-to-the-vm"></a>VM に接続します
作成した SQL Server VM には、SQL Server Management Studio (SSMS) などのツールやアプリケーションから接続することができます。 その手順については、「[Azure での SQL Server 仮想マシンへの接続](virtual-machines-windows-sql-connect.md)」を参照してください。

### <a name="migrate-your-data"></a>データの移行
既存のデータベースがある場合は、新たにプロビジョニングした SQL VM にそれを移動することがあります。 移行オプションの一覧とガイダンスについては、 [Azure VM の SQL Server へのデータベースの移行](virtual-machines-windows-migrate-sql.md)に関するページを参照してください。

## <a id="lifecycle"></a>SQL VM イメージの更新ポリシー
Azure に保持される仮想マシン イメージは、サポートされるオペレーティング システム、バージョン、エディションの組み合わせごとに 1 つだけです。 つまりイメージは、いずれ更新され、古いイメージは削除されていくことになります。 詳細については、[SQL Server VM の FAQ](virtual-machines-windows-sql-server-iaas-faq.md#images) に関するページの「**イメージ**」セクションを参照してください。

## <a name="customer-experience-improvement-program-ceip"></a>カスタマー エクスペリエンス向上プログラム (CEIP)
既定では、カスタマー エクスペリエンス向上プログラム (CEIP) が有効です。 これにより、レポートが定期的に Microsoft に送信され、SQL Server の改善に役立ちます。 プロビジョニング後に無効にする場合を除き、CEIP で必要な管理タスクはありません。 リモート デスクトップで VM に接続することで、CEIP をカスタマイズしたり無効にしたりすることができます。 その後、 **SQL Server エラーと使用状況レポート** ユーティリティを実行します。 レポートを無効にするには、指示に従います。 データの収集の詳細については、「[SQL Server のプライバシーに関する声明](https://docs.microsoft.com/sql/getting-started/microsoft-sql-server-privacy-statement)」を参照してください。

## <a name="related-products-and-services"></a>関連製品およびサービス
### <a name="windows-virtual-machines"></a>Windows Virtual Machines
* [Virtual Machines の概要](../overview.md)

### <a name="storage"></a>Storage
* [Microsoft Azure Storage の概要](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>ネットワーク
* [仮想ネットワークの概要](../../../virtual-network/virtual-networks-overview.md)
* [Azure 内の IP アドレス](../../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Azure Portal での完全修飾ドメイン名の作成](../portal-create-fqdn.md)

### <a name="sql"></a>SQL
* [SQL Server ドキュメント](https://docs.microsoft.com/sql/index)
* [Azure SQL Database の比較](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

## <a name="next-steps"></a>次の手順

Azure Virtual Machines における SQL Server の概要

* [Azure Portal での SQL Server VM の作成](quickstart-sql-vm-create-portal.md)

SQL VM についてよく寄せられる質問とその回答

* [Azure Virtual Machines における SQL Server に関する FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
