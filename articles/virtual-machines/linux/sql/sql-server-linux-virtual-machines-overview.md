---
title: Azure Linux 仮想マシンにおける SQL Server の概要 | Microsoft Docs
description: Azure の Linux 仮想マシンで各 SQL Server エディションを実行する方法について説明します。 すべての Linux SQL Server VM イメージと関連コンテンツへのダイレクト リンクが記載されています。
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: d376672336845958fb6434a78177f42aca938229
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70081963"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Azure 仮想マシン (Linux) における SQL Server の概要

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](sql-server-linux-virtual-machines-overview.md)

Azure 仮想マシン上の SQL Server では、オンプレミスのハードウェアを一切管理することなく全バージョンの SQL Server をクラウドで利用することができます。 また、SQL Server VM によって従量課金制のライセンス料が単純化されます。

Azure 仮想マシンは、世界中のさまざまな[リージョン](https://azure.microsoft.com/regions/)で実行されます。 また、[マシンのサイズ](../sizes.md)も各種用意されています。 適切なバージョン、エディション、オペレーティング システムの SQL Server VM を仮想マシン イメージ ギャラリーで作成することができます。 さまざまな SQL Server ワークロードに合った最適な仮想マシンを選ぶことができます。

## <a id="create"></a>SQL VM を使ってみる

まず、必要なバージョン、エディション、オペレーティング システムの SQL Server 仮想マシン イメージを選んでください。 以下のセクションでは、SQL Server 仮想マシン ギャラリー イメージに関する、Azure Portal へのリンクを記載しています。

> [!TIP]
> SQL イメージの価格の詳細については、[Linux SQL Server VM の価格](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)に関するページを参照してください。

| Version | オペレーティング システム | エディション |
| --- | --- | --- |
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)、[Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74)、[Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)、[Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2)、[Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS)、[Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)、[Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)、[Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS)、[Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> 利用できる Windows SQL Server 仮想マシン イメージを確認するには、「[Azure Virtual Machines における SQL Server の概要 (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)」を参照してください。

## <a id="packages"></a> インストール済みパッケージ

Linux に SQL Server を構成するときには、データベース エンジン パッケージをインストールしてから、要件に応じてオプションのパッケージをインストールします。 ほとんどのパッケージは、SQL Server の Linux 仮想マシン イメージにより自動でインストールされます。 以下の表は、各ディストリビューションでインストールされているパッケージを示したものです。

| ディストリビューション | [データベース エンジン](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [ツール](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [SQL Server エージェント](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [フルテキスト検索](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [HA アドオン](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![はい](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![はい](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![はい](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![はい](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![はい](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![×](./media/sql-server-linux-virtual-machines-overview/no.png) |
| SLES | ![はい](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![はい](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![はい](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![はい](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![×](./media/sql-server-linux-virtual-machines-overview/no.png) | ![×](./media/sql-server-linux-virtual-machines-overview/no.png) |
| Ubuntu | ![はい](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![はい](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![はい](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![はい](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![はい](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![はい](./media/sql-server-linux-virtual-machines-overview/yes.png) |

## <a name="related-products-and-services"></a>関連製品およびサービス

### <a name="linux-virtual-machines"></a>Linux Virtual Machines

* [Virtual Machines の概要](../overview.md)

### <a name="storage"></a>Storage

* [Microsoft Azure Storage の概要](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>ネットワーク

* [仮想ネットワークの概要](../../../virtual-network/virtual-networks-overview.md)
* [Azure 内の IP アドレス](../../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Azure Portal での完全修飾ドメイン名の作成](../portal-create-fqdn.md)

### <a name="sql"></a>SQL

* [SQL Server on Linux のドキュメント](https://docs.microsoft.com/sql/linux)
* [Azure SQL Database の比較](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

## <a name="next-steps"></a>次の手順

Azure Linux 仮想マシン上の SQL Server の概要:

* [Azure Portal での SQL Server VM の作成](provision-sql-server-linux-virtual-machine.md)

Linux 上の SQL VM についてよく寄せられる質問とその回答:

* [Azure Linux Virtual Machines における SQL Server に関する FAQ](sql-server-linux-faq.md)
