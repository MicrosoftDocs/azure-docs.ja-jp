---
title: Azure VM の SQL Server リリース ノート | Microsoft Docs
description: Azure VM 上の SQL Server の新機能と機能改善について説明します。
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 2/13/2019
ms.openlocfilehash: ee3aeb9f44d1b98d6307c6a72d1e4786ea1ec664
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076899"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Azure 仮想マシンの SQL Server リリース ノート

Azure では、SQL Server のイメージを組み込んだ仮想マシンをデプロイできます。 この記事は、[Azure 仮想マシン上の SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) の最新リリースで導入された新機能と機能強化をまとめたものです。 この記事では、今回のリリースとは直接関連しないものの、同じタイム フレームで公開された注目すべきコンテンツの更新についても一覧表示しています。 他の Azure サービスの機能強化については、「[サービスの更新情報](https://azure.microsoft.com/updates)」を参照してください

## <a name="may-2019"></a>2019 年 5 月

### <a name="service-improvements"></a>サービスの機能強化

| サービスの機能強化 | 詳細 |
| --- | --- |
| **Azure portal での新しい SQL VM の管理** | Azure portal で SQL Server VM を管理する新しい方法が導入されました。 詳しくは、「[Azure portal で SQL Server VM を管理する](virtual-machines-windows-sql-manage-portal.md)」をご覧ください。  | 
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>ドキュメントの改善

| ドキュメント | 詳細 |
| --- | --- |
| **新しい SQL VM ポータル管理** | 新しい SQL VM 管理ポータル エクスペリエンスに対して約 10 件の記事が更新されました。 | 
| &nbsp; | &nbsp; |


## <a name="april-2019"></a>2019 年 4 月

### <a name="service-improvements"></a>サービスの機能強化

| サービスの機能強化 | 詳細 |
| --- | --- |
| **SQL Server 2008/2008R2 のサポート延長** | "*そのまま*" Azure VM に移行することで、SQL Server 2008 および SQL Server 2008 R2 の[サポートを延長](virtual-machines-windows-sql-server-2008-eos-extend-support.md)します。 | 
| &nbsp; | &nbsp; |


## <a name="march-2019"></a>2019 年 3 月

| サービスの機能強化 | 詳細 |
| --- | --- |
| **カスタム イメージのサポートの可否** | OS と SQL イメージをカスタマイズするために、[SQL IaaS 拡張機能](virtual-machines-windows-sql-server-agent-extension.md#installation)をインストールできるようになりました。これにより、機能が制限された[柔軟なライセンス](virtual-machines-windows-sql-ahb.md)が提供されます。 SQL リソース プロバイダーでカスタム イメージを登録するときに、'AHUB' としてライセンスの種類を指定します。それ以外の場合は、登録は失敗します。  | 
| **名前付きインスタンスのサポートの可否** | 既定のインスタンスが適切にアンインストールされている場合、名前付きインスタンスで [SQL IaaS 拡張機能](virtual-machines-windows-sql-server-agent-extension.md#installation)を使用できるようになりました。 | 
| **ポータルの機能強化** | SQL Server VM をデプロイするための Azure portal エクスペリエンスは、より使いやすくなるように改良されています。 詳細については、SQL Server VM のデプロイに関する簡単な[クイック スタート](quickstart-sql-vm-create-portal.md)と、より包括的な[ハウツー](virtual-machines-windows-portal-sql-server-provision.md) ガイドを参照してください。|
| &nbsp; | &nbsp; |


## <a name="february-2019"></a>2019 年 2 月

| サービスの機能強化 | 詳細 |
| --- | --- |
| **ポータルの改善** | [Azure portal](virtual-machines-windows-sql-ahb.md#with-the-azure-portal-1) を使用して、SQL Server VM のライセンス モデルを従量課金制からライセンス持ち込みに変更できるようになりました。|
|**Azure SQL VM の CLI を使用した可用性グループ展開の簡略化** | 可用性グループを Azure の SQL Server VM に展開することは、以前より簡単になりました。 [Azure SQL VM の CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) を使用して、WSFC、ILB、AG リスナーをすべてコマンドラインから、記録的な速さで作成できます。 詳細については、「[Use Azure SQL VM CLI to configure Always On availability group for SQL Server on an Azure VM (Azure SQL VM CLI を使用して Azure VM 上で SQL Server のための Always On 可用性グループを構成する)](virtual-machines-windows-sql-availability-group-cli.md)」を参照してください。 | 
| &nbsp; | &nbsp; |


## <a name="december-2018"></a>2018 年 12 月

| サービスの機能強化 | 詳細 |
| --- | --- |
| **新しい SQL クラスター グループ リソース プロバイダー** | Windows フェールオーバー クラスターのメタデータを定義する新しいリソース プロバイダー (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups)。 SQL Server VM を *SqlVirtualMachineGroups* に参加させることで、Windows フェールオーバー クラスター サービスをブートストラップし、VM をクラスターに参加させます。  |
|**Azure クイック スタート テンプレートを使用して、可用性グループのデプロイの設定を自動化する** |2 つの Azure クイック スタート テンプレートを使用して、Windows フェールオーバー クラスターを作成し、SQL Server VM をそれに参加させ、リスナーを作成し、内部ロード バランサーを構成することができるようになりました。 詳細については、「[Use Azure Quickstart Template to configure Always On availability group for SQL Server on an Azure VM (Azure クイック スタート テンプレートを使用して Azure VM 上で SQL Server のための Always On 可用性グループを構成する)](virtual-machines-windows-sql-availability-group-quickstart-template.md)」を参照してください。 | 
| **SQL VM リソース プロバイダーの自動登録** | 今月以降にデプロイされる SQL Server VM は、自動的に新しい SQL Server リソース プロバイダーに登録されます。 今月より前にデプロイされた SQL Server VM は、まだ手動で登録する必要があります。 詳細については、「[Register existing SQL VM with SQL VM resource provider (既存の SQL VM を SQL VM リソース プロバイダーに登録する)](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider)」をご覧ください。|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>2018 年 11 月

| サービスの機能強化 | 詳細 |
| --- | --- |
| **新しい SQL VM リソース プロバイダー** |  SQL Server VM の管理が向上する、SQL Server VM 用の新しいリソース プロバイダー (Microsoft.SqlVirtualMachine)。 自分の VM を登録する方法の詳細については、「[既存の SQL VM を新しいリソース プロバイダーに登録する](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider)」を参照してください。 |
|**ライセンス モデルを切り替える** |Azure CLI または PowerShell を使用して、SQL VM のライセンス モデルを従量制と持ち込みの間で切り替えることができます。 詳細については、[SQL VM のライセンス モデルを変更する方法](virtual-machines-windows-sql-ahb.md)に関するページを参照してください。 | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>その他のリソース

**Windows VM**:

* [Windows VM における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server Windows VM のプロビジョニング](virtual-machines-windows-portal-sql-server-provision.md)
* [Azure VM の SQL Server へのデータベースの移行](virtual-machines-windows-migrate-sql.md)
* [Azure 仮想マシンにおける SQL Server の高可用性と障害復旧](virtual-machines-windows-sql-high-availability-dr.md)
* [Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス](virtual-machines-windows-sql-performance.md)
* [Azure Virtual Machines における SQL Server のアプリケーション パターンと開発計画](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux VM**:

* [Linux VM における SQL Server の概要](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [SQL Server Linux 仮想マシンのプロビジョニング](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [よく寄せられる質問 (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [SQL Server on Linux のドキュメント](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
