---
title: SQL Server IaaS Agent 拡張機能とは
description: この記事では、SQL Server IaaS Agent 拡張機能を使用して、Azure VM 上の SQL Server の管理固有の管理タスクを自動化する方法について説明します。 この中には、自動バックアップ、自動修正、Azure Key Vault の統合、ライセンス管理、ストレージ構成、すべての SQL Server VM インスタンスの中央管理といった機能が含まれます。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: fdff3f6144f7099f3f61cfe57186357e17136e9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103225491"
---
# <a name="automate-management-with-the-sql-server-iaas-agent-extension"></a>SQL Server IaaS Agent 拡張機能を使用して管理を自動化する
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


SQL Server IaaS Agent 拡張機能 (SQLIaaSExtension) は、管理タスクを自動化するために Azure 仮想マシン (VM) 上の SQL Server で実行されます。 

この記事では、この拡張機能の概要について説明します。 SQL Server IaaS 拡張機能を Azure VM 上の SQL Server にインストールする方法については、[自動インストール](sql-agent-extension-automatic-registration-all-vms.md)、 [単一の VM の登録](sql-agent-extension-manually-register-single-vm.md)、または [VM の一括登録](sql-agent-extension-manually-register-vms-bulk.md) に関する記事を参照してください。 

## <a name="overview"></a>概要

SQL Server IaaS Agent 拡張機能を使用すると、Azure portal との統合が可能になり、管理モードによっては、Azure VM 上の SQL Server のいくつかの機能の利点が明らかになります。 

- **機能面の利点**: この拡張機能では、ポータル管理、ライセンスの柔軟性、自動バックアップ、自動修正といった、自動化機能のさまざまな利点を活用できます。 詳細については、この記事の後半にある「[機能面の利点](#feature-benefits)」を参照してください。 

- **コンプライアンス**:この拡張機能では、製品の条件に指定されているとおりに Azure ハイブリッド特典が有効になっていることを Microsoft に通知する要件を満たす簡単な方法が提供されます。 このプロセスにより、リソースごとにライセンス登録フォームを管理する必要がなくなります。  

- **Free**:3 つすべての管理モードの拡張機能は、完全に無料です。 拡張機能または管理モードの変更に関連する追加のコストは発生しません。 

- **簡略化されたライセンス管理**:この拡張機能では、SQL Server ライセンスの管理が簡略化されるほか、[Azure portal](manage-sql-vm-portal.md)、PowerShell または Azure CLI を使用して、Azure ハイブリッド特典が有効になった SQL Server VM をすばやく識別することができます。 

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $ az sql vm list --query "[?sqlServerLicenseType=='AHUB']"
   ```
   ---


> [!IMPORTANT]
> SQL IaaS Agent 拡張機能を使用すると、Azure 仮想マシン内で SQL Server を使用する際に、お客様に追加のメリットを提供するという明確な目的のためにデータが収集されます。 Microsoft は、お客様の事前の同意なく、ライセンスの監査にこのデータを使用することはありません。 詳細については、「[SQL Server のプライバシーの補足情報](/sql/sql-server/sql-server-privacy#non-personal-data)」を参照してください。


## <a name="feature-benefits"></a>機能面の利点 

SQL Server IaaS Agent 拡張機能には、SQL Server VM を管理するための機能面の利点が複数あります。 

次の表に、これらの利点の詳細を示します。 


| 機能 | 説明 |
| --- | --- |
| **ポータル管理** | [ポータルで管理することができます](manage-sql-vm-portal.md)。このため、すべての SQL Server VM を 1 か所に表示でき、ポータルから SQL 固有の機能を直接有効または無効にすることができます。 <br/> 管理モード: Lightweight および完全|  
| **自動バックアップ** |VM 上の SQL Server の既定のインスタンスまたは[適切にインストールされた](frequently-asked-questions-faq.md#administration)名前付きインスタンスについて、すべてのデータベースのバックアップを自動的にスケジュールします。 詳細については、[Azure 仮想マシンでの SQL Server の自動バックアップ (Resource Manager)](automated-backup-sql-2014.md) に関する記事を参照してください。 <br/> 管理モード: [完全]|
| **自動修正** |VM で Windows と SQL Server の重要なセキュリティ更新プログラムを実行できるメンテナンス期間を構成します。こうすることで、ワークロードのピーク時に更新が実行されるのを回避できます。 詳細については、「[Azure Virtual Machines での SQL Server の自動修正 (Resource Manager)](automated-patching.md)」をご覧ください。 <br/> 管理モード: [完全]|
| **Azure Key Vault の統合** |SQL Server VM に Azure Key Vault を自動的にインストールして構成できます。 詳細については、「[Azure Virtual Machines 上の SQL Server 向け Azure Key Vault 統合の構成 (Resource Manager)](azure-key-vault-integration-configure.md)」をご覧ください。 <br/> 管理モード: [完全]|
| **ポータルでディスク使用率を表示する** | Azure portal 内の SQL データ ファイルのディスク使用率をグラフィカルに表示できます。  <br/> 管理モード: [完全] | 
| **柔軟なライセンス** | ライセンス持ち込み (「Azure ハイブリッド特典」とも呼ばれます) から従量課金制のライセンス モデルに、またはその逆に[シームレスに移行](licensing-model-azure-hybrid-benefit-ahb-change.md)することで、コストを節約できます。 <br/> 管理モード: Lightweight および完全| 
| **柔軟なバージョン/エディション** | SQL Server の[バージョン](change-sql-server-version.md)または[エディション](change-sql-server-edition.md)を変更する場合は、SQL Server VM 全体を再デプロイすることなく、Azure portal 内のメタデータを更新できます。  <br/> 管理モード: Lightweight および完全| 


## <a name="management-modes"></a>管理モード

次の 3 つの管理モードによる SQL IaaS 拡張機能の登録を選択できます。 

- **軽量** モードでは、拡張機能バイナリが VM にコピーされますが、エージェントはインストールされず、SQL Server サービスは再起動されません。 軽量モードでは、SQL Server のライセンスの種類とエディションの変更のみがサポートされ、ポータルの管理が制限されます。 このオプションは、複数のインスタンスがあるか、フェールオーバー クラスター インスタンス (FCI) に参加している SQL Server VM に使用します。 簡易モードは、[自動登録](sql-agent-extension-automatic-registration-all-vms.md)機能を使用する場合、または手動登録時に管理の種類が指定されていない場合の既定の管理モードです。 軽量モードを使用する場合、メモリまたは CPU への影響はなく、また関連コストはありません。 最初に軽量モードで SQL Server VM を登録してから、予定メンテナンス期間中にフル モードにアップグレードすることをお勧めします。 

- **フル** モードでは、SQL IaaS Agent が VM にインストールされてすべての機能が提供されますが、SQL Server サービスを再起動する必要があり、またシステム管理者権限が必要です。 単一のインスタンスがある SQL Server VM を管理する場合に使用します。 フル モードでは、メモリと CPU に与える影響が最小の 2 つの Windows サービスがインストールされます。これらは、タスク マネージャーを通して監視できます。 完全管理モードの使用に関連するコストはありません。 

- **NoAgent** モードは Windows Server 2008 にインストールされた SQL Server 2008 および SQL Server 2008 R2 の専用モードです。 NoAgent モードを使用する場合、メモリまたは CPU への影響はありません。 NoAgent 管理モードの使用に関連するコストはありません。SQL Server は再起動されず、エージェントは VM にインストールされません。 

Azure PowerShell を次のように使用して、SQL Server IaaS Agent の現在のモードを確認できます。 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```


## <a name="installation"></a>インストール

SQL Server VM を SQL Server IaaS Agent 拡張機能に登録し、仮想マシン リソースとは "_別の_" リソースである、**SQL 仮想マシン** "_リソース_" をサブスクリプション内に作成します。 拡張機能から SQL Server VM を登録解除すると、**SQL 仮想マシン** "_リソース_" は削除されますが、実際の仮想マシンは削除されません。

Azure portal を介して SQL Server VM の Azure Marketplace イメージをデプロイすると、その SQL Server VM が自動的に拡張機能に登録されます。 ただし、Azure 仮想マシンに SQL Server を自分でインストールすること、またはカスタム VHD から Azure 仮想マシンをプロビジョニングすることを選択する場合は、機能面の利点を活用できるようにするために、SQL Server VM を SQL IaaS Agent 拡張機能に登録する必要があります。 

軽量モードで拡張機能を登録すると、バイナリはコピーされますが、エージェントは VM にインストールされません。 拡張機能が完全管理モードにアップグレードされると、エージェントは VM にインストールされます。 

拡張機能に登録する方法は 3 つあります。 
- [サブスクリプション内の現在の VM および今後の VM をすべて自動登録する場合](sql-agent-extension-automatic-registration-all-vms.md)
- [単一の VM を手動で](sql-agent-extension-manually-register-single-vm.md)
- [複数の VM を一括で手動で](sql-agent-extension-manually-register-vms-bulk.md)

### <a name="named-instance-support"></a>名前付きインスタンスのサポート

SQL Server IaaS Agent 拡張機能は、それが仮想マシンで使用できる唯一の SQL Server インスタンスである場合に、SQL Server の名前付きインスタンスを使用して動作します。 VM 上に既定のインスタンスが存在しない場合、拡張機能は複数の名前付き SQL Server インスタンスを持つ VM にインストールできません。 

SQL Server の名前付きインスタンスを使用するには、Azure 仮想マシンをデプロイし、1 つの名前付き SQL Server インスタンスをそれにインストールし、その後、[SQL IaaS 拡張機能](sql-agent-extension-manually-register-single-vm.md)にそれを登録します。

または、Azure Marketplace SQL Server イメージで名前付きインスタンスを使用するには、次の手順を実行します。 

   1. Azure Marketplace から SQL Server VM をデプロイします。 
   1. SQL IaaS Agent 拡張機能から SQL Server VM の[登録を解除](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension)します。 
   1. SQL Server VM 内で SQL Server を完全にアンインストールします。
   1. SQL Server VM 内の名前付きインスタンスで SQL Server をインストールします。 
   1. [SQL IaaS Agent 拡張機能に VM を登録します](sql-agent-extension-manually-register-single-vm.md#register-with-extension)。 

## <a name="verify-status-of-extension"></a>拡張機能の状態を確認する

Azure portal または Azure PowerShell を使用して、拡張機能の状態を確認します。 

### <a name="azure-portal"></a>Azure portal

Azure portal に拡張機能がインストールされていることを確認します。 

仮想マシンのウィンドウで **[すべての設定]** を選択し、 **[拡張機能]** を選択します。 **SqlIaasExtension** 拡張機能が一覧表示されます。

![Azure portal での SQL Server IaaS Agent 拡張機能の状態](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)


### <a name="azure-powershell"></a>Azure PowerShell

**Get-AzVMSqlServerExtension** Azure PowerShell コマンドレットを使用することもできます。

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

前のコマンドでは、エージェントがインストールされていることが確認され、全般的な状態情報が提供されます。 次のコマンドを使用して、自動バックアップと自動修正に関する特定の状態情報を取得できます。

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```


## <a name="limitations"></a>制限事項

SQL IaaS Agent 拡張機能では、以下のみがサポートされます。 

- Azure Resource Manager を介してデプロイされた SQL Server VM。 クラシック モデルを介してデプロイされた SQL Server VM はサポートされません。 
- パブリック クラウドまたは Azure Government クラウドにデプロイされた SQL Server VM。 その他のプライベート クラウドや政府機関向けクラウドにデプロイされている場合はサポートされません。 


## <a name="in-region-data-residency"></a>リージョンのデータ所在地
Azure SQL 仮想マシンと SQL IaaS Agent 拡張機能では、デプロイされているリージョンから顧客データを移動または保存することはできません。

## <a name="next-steps"></a>次のステップ

SQL Server IaaS 拡張機能を Azure VM 上の SQL Server にインストールする方法については、[自動インストール](sql-agent-extension-automatic-registration-all-vms.md)、 [単一の VM の登録](sql-agent-extension-manually-register-single-vm.md)、または [VM の一括登録](sql-agent-extension-manually-register-vms-bulk.md) に関する記事を参照してください。

Azure Virtual Machines で SQL Server を実行する方法の詳細については、「[Azure Virtual Machines における SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)」を参照してください。

詳細については、[よく寄せられる質問](frequently-asked-questions-faq.md)を参照してください。 
