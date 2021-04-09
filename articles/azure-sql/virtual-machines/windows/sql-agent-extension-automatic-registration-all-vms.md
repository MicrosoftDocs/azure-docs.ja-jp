---
title: SQL IaaS Agent 拡張機能への自動登録
description: Azure portal を使用して自動登録機能を有効にし、既存および今後のすべての SQL Server VM を SQL IaaS Agent 拡張機能に自動的に登録する方法について説明します。
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.openlocfilehash: 139852949a3744fd603cb197b2e27fa32679aae0
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042422"
---
# <a name="automatic-registration-with-sql-iaas-agent-extension"></a>SQL IaaS Agent 拡張機能への自動登録
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure portal で自動登録機能を有効にすると、Azure Virtual Machines (VM) 上の既存および今後のすべての SQL Server は軽量モードで [SQL IaaS Agent 拡張機能](sql-server-iaas-agent-extension-automate-management.md)に自動的に登録されます。 

この記事では、自動登録機能を有効にする方法について説明します。 また、SQL IaaS Agent 拡張機能に [1 つの VM を登録する](sql-agent-extension-manually-register-single-vm.md)ことや、[複数の VM を一括で登録する](sql-agent-extension-manually-register-vms-bulk.md)こともできます。 

## <a name="overview"></a>概要

ご使用の SQL Server VM を [SQL IaaS Agent 拡張機能](sql-server-iaas-agent-extension-automate-management.md)に登録すると、特典の完全な機能セットがロック解除されます。 

自動登録が有効になっている場合、サブスクリプション内のすべての未登録の VM に SQL Server がインストールされているかどうかを検出するため、ジョブが毎日実行されます。 これを行うには、SQL IaaS Agent 拡張機能バイナリを VM にコピーし、SQL Server レジストリ ハイブをチェックする 1 回限りのユーティリティを実行します。 SQL Server ハイブが検出された場合、仮想マシンは軽量モードで拡張機能に登録されます。 レジストリに SQL Server ハイブが存在しない場合は、バイナリが削除されます。

サブスクリプションに対して自動登録を有効にすると、SQL Server が既にインストールされている、または今後インストールされる VM が、**軽量モードでダウンタイムがなく、また、SQL Server サービスを再起動することなく**、SQL IaaS Agent 拡張機能に登録されます。 完全な機能セットを使用するには、[完全管理モードに手動でアップグレードする](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full)必要があります。 

> [!IMPORTANT]
> SQL IaaS Agent 拡張機能を使用すると、Azure 仮想マシン内で SQL Server を使用する際に、お客様に追加のメリットを提供するという明確な目的のためにデータが収集されます。 Microsoft は、お客様の事前の同意なく、ライセンスの監査にこのデータを使用することはありません。 詳細については、「[SQL Server のプライバシーの補足情報](/sql/sql-server/sql-server-privacy#non-personal-data)」を参照してください。

## <a name="prerequisites"></a>前提条件

SQL Server VM を拡張機能に登録するには、以下のものが必要になります。 

- [Azure サブスクリプション](https://azure.microsoft.com/free/)と、少なくとも[共同作成者ロール](../../../role-based-access-control/built-in-roles.md#all)のアクセス許可。
- パブリック クラウドまたは Azure Government クラウドにデプロイされた Azure Resource Model の [Windows Server 2008 R2 (またはそれ以降) の仮想マシン](../../../virtual-machines/windows/quick-create-portal.md)と [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads)。 Windows Server 2008 はサポートされていません。 


## <a name="enable"></a>有効化

Azure portal で SQL Server VM の自動登録を有効にするには、これらの手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. [ **[SQL 仮想マシン]**](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) リソース ページに移動します。 
1. **[Automatic SQL Server VM registration]\(自動 SQL Server VM 登録\)** を選択して **[Automatic registration]\(自動登録\)** ページを開きます。 

   :::image type="content" source="media/sql-agent-extension-automatic-registration-all-vms/automatic-registration.png" alt-text="[Automatic SQL Server VM registration]\(自動 SQL Server VM 登録\) を選択して [Automatic registration]\(自動登録\) ページを開きます":::

1. ドロップダウンから使用するサブスクリプションを選択します。 
1. 条項を読み、同意する場合は **[同意する]** を選択します。 
1. **[登録]** を選択すると、この機能が有効になり、既存および今後のすべての SQL Server VM が SQL IaaS Agent 拡張機能に自動的に登録されるようになります。 これにより、どの VM 上の SQL Server サービスも再起動されません。 

## <a name="disable"></a>無効

自動登録機能を無効にするには、[Azure CLI](/cli/azure/install-azure-cli) または [Azure PowerShell](/powershell/azure/install-az-ps) を使用します。 自動登録機能が無効になっている場合、サブスクリプションに追加された SQL Server VM は、SQL IaaS Agent 拡張機能に手動で登録する必要があります。 これにより、既に登録されている既存の SQL Server VM の登録が解除されることはありません。



# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して自動登録を無効にするには、次のコマンドを実行します。 

```azurecli-interactive
az feature unregister --namespace Microsoft.SqlVirtualMachine --name BulkRegistration
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure PowerShell を使用して自動登録を無効にするには、次のコマンドを実行します。 

```powershell-interactive
Unregister-AzProviderFeature -FeatureName BulkRegistration -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="enable-for-multiple-subscriptions"></a>複数のサブスクリプションに対して有効にする

PowerShell を使用すると、複数の Azure サブスクリプションの自動登録機能を有効にすることができます。 

これを行うには、次の手順に従います。

1. [このスクリプト](https://github.com/microsoft/tigertoolbox/blob/master/AzureSQLVM/RegisterSubscriptionsToSqlVmAutomaticRegistration.ps1)を `EnableBySubscription.ps1` などの `.ps1` ファイルに保存します。 
1. 管理コマンド プロンプトまたは PowerShell ウィンドウを使用して、スクリプトを保存した場所に移動します。 
1. Azure に接続します (`az login`)。
1. スクリプトを実行し、SubscriptionIds を次のようなパラメーターとして渡します。   
   `.\EnableBySubscription.ps1 -SubscriptionList SubscriptionId1,SubscriptionId2`

   次に例を示します。 

   ```console
   .\EnableBySubscription.ps1 -SubscriptionList a1a1a-aa11-11aa-a1a1-a11a111a1,b2b2b2-bb22-22bb-b2b2-b2b2b2bb
   ```

登録の失敗エラーは、`.ps1` スクリプトの保存と実行に使用したものと同じディレクトリ内にある `RegistrationErrors.csv` に格納されます。 

## <a name="next-steps"></a>次のステップ

管理モードを[完全](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full)にアップグレードして、SQL IaaS Agent 拡張機能によって提供される完全な機能セットを使用します。 
