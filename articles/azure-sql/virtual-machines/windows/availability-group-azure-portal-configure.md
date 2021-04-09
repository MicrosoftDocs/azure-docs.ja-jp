---
title: 可用性グループの構成 (Azure portal)
description: Azure portal を使用して、Azure の SQL Server VM で Windows フェールオーバー クラスター、可用性グループ リスナー、および内部ロード バランサーを作成します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/20/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019, devx-track-azurecli
ms.openlocfilehash: 14760b4244d42e57aaed7f7d96f487a66147a554
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359508"
---
# <a name="use-azure-portal-to-configure-an-availability-group-preview-for-sql-server-on-azure-vm"></a>Azure portal を使用し、Azure VM 上で SQL Server の可用性グループ (プレビュー) を構成する 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

この記事では、[Azure portal](https://portal.azure.com) を使用して、Azure VM 上に SQL Server の可用性グループを構成する方法について説明します。 

Azure portal を使用して新しいクラスターを作成するか、既存のクラスターをオンボードしてから、可用性グループ、リスナー、および内部ロード バランサーを作成します。 

現在、この機能はプレビュー段階にあります。 

この記事では Azure portal を使用して可用性グループ環境を構成しますが、[PowerShell または Azure CLI](availability-group-az-commandline-configure.md) を使用して構成するか、[Azure クイックスタート テンプレート](availability-group-quickstart-template-configure.md)を使用して構成するか、[手動](availability-group-manually-configure-tutorial.md)で構成することもできます。 


## <a name="prerequisites"></a>前提条件

Azure portal を使用して Always On 可用性グループを構成するには、次の前提条件を満たしている必要があります。 

- [Azure サブスクリプション](https://azure.microsoft.com/free/)。
- ドメイン コントローラーを含むリソース グループ。 
- [完全管理モードで SQL IaaS Agent 拡張機能に登録](sql-agent-extension-manually-register-single-vm.md)され、各 VM 上の SQL Server サービスで同じドメイン アカウントを使用している、"*同じ*" 可用性セットまたは "*異なる*" 可用性ゾーンにあって 1 つ以上のドメイン参加済みの [SQL Server 2016 (またはそれ以降の) Enterprise エディションを実行している Azure の VM](./create-sql-vm-portal.md)。
- 2 つの使用可能な (どのエンティティでも使用されていない) IP アドレス。 1 つは内部ロード バランサー用です。 もう 1 つは、可用性グループと同じサブネット内の可用性グループ リスナー用です。 既存のロード バランサーを使用している場合は、使用可能な IP アドレスが可用性グループ リスナー用に 1 つだけ必要です。 

## <a name="permissions"></a>アクセス許可

Azure portal を使用して可用性グループを構成するには、次のアカウントのアクセス許可が必要です。 

- ドメインで **コンピューター オブジェクトを作成する** ためのアクセス許可を持っている既存のドメイン ユーザー アカウント。 たとえばドメイン管理者アカウントは、一般に十分なアクセス許可を持っています (例: account@domain.com)。 _クラスターを作成するには、このアカウントが、各 VM でローカル管理者グループに含まれている必要もあります。_
- SQL Server を制御するドメイン ユーザー アカウント。 これは、可用性グループに追加するすべての SQL Server VM で同じアカウントである必要があります。 

## <a name="configure-cluster"></a>クラスターの構成

Azure portal を使用してクラスターを構成します。 新しいクラスターを作成するか、既存のクラスターがある場合は、それを SQL IaaS Agent 拡張機能に追加することでポータルでの管理が容易になります。


### <a name="create-a-new-cluster"></a>新しいクラスターを作成する

すでにクラスターがある場合は、このセクションをスキップし、代わりに「[既存のクラスターのオンボード](#onboard-existing-cluster)」に移動します。 

既存のクラスターがない場合は、次の手順で Azure portal を使用してクラスターを作成します。

1. [Azure Portal](https://portal.azure.com) にサインインします。 
1. [SQL 仮想マシン](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) リソースに移動します。 
1. **[設定]** の **[高可用性]** を選択します。 
1. **[+ New Windows Server failover cluster]\(+ 新しい Windows Server フェールオーバー クラスター\)** を選択して **[Configure Windows Failover cluster]\(Windows フェールオーバー クラスターを構成する\)** ページを開きます。  

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-cluster.png" alt-text="ポータルで [+ new cluster]\(+ 新しいクラスター\) を選択して新しいクラスターを作成する":::

1. クラスターに名前を付け、クラウド監視として使用するストレージ アカウントを指定します。 既存のストレージ アカウントを使用するか、 **[新規作成]** を選択して新しいストレージ アカウントを作成します。 ストレージ アカウント名は、長さが 3 文字から 24 文字で、数字と小文字だけを使用できます。

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-1.png" alt-text="クラスターの名前、ストレージ アカウント、および資格情報を指定する":::

1. **[Windows Server Failover Cluster credentials]\(Windows Server フェールオーバー クラスターの資格情報\)** を展開して、SQL Server サービス アカウントの [資格情報](/rest/api/sqlvm/sqlvirtualmachinegroups/createorupdate#wsfcdomainprofile)を指定し、さらにクラスター オペレーターとブートストラップ アカウントが SQL Server サービスに使用されるアカウントとは異なる場合は、これらも指定します。 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-2.png" alt-text="SQL サービス アカウント、クラスター オペレーター アカウント、およびクラスター ブートストラップ アカウントの資格情報を入力する":::

1. クラスターに追加する SQL Server VM を選択します。 再起動が必要かどうかを確認し、慎重に進めてください。 完全管理モードで SQL IaaS Agent 拡張機能に登録され、プライマリ SQL Server VM と同じ場所、ドメイン、および同じ仮想ネットワーク上にある VM だけが表示されます。 
1. **[適用]** を選択してクラスターを作成します。 デプロイの状態は、上部のナビゲーション バーにあるベルのアイコンからアクセスできる **[アクティビティ ログ]** で確認できます。 
1. Microsoft によってフェールオーバー クラスターがサポートされるためには、クラスター検証に合格する必要があります。 任意の方法 (リモート デスクトップ プロトコル (RDP) など) を使用して VM に接続し、先に進む前に、クラスターが検証に合格していることを確認します。 これに失敗すると、クラスターはサポートされていない状態のままになります。 フェールオーバー クラスター マネージャー (FCM) または次の PowerShell コマンドを使用して、クラスターを検証できます。

    ```powershell
    Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
    ```
    


### <a name="onboard-existing-cluster"></a>既存のクラスターのオンボード

SQL Server VM 環境でクラスターがすでに構成されている場合は、Azure portal からオンボードできます。

これを行うには、次のステップに従います。

1. [Azure Portal](https://portal.azure.com) にサインインします。 
1. [SQL 仮想マシン](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) リソースに移動します。 
1. **[設定]** の **[高可用性]** を選択します。 
1. **[Onboard existing Windows Server Failover Cluster]\(既存の Windows Server フェールオーバー クラスターのオンボード\)** を選択して **[Onboard Windows Server Failover Cluster]\(Windows Server フェールオーバー クラスターのオンボード\)** ページを開きます。 

   :::image type="content" source="media/availability-group-az-portal-configure/onboard-existing-cluster.png" alt-text="SQL 仮想マシン リソースの [高可用性] ページから既存のクラスターをオンボードする":::

1. クラスターの設定を確認します。 
1. **[適用]** を選択してクラスターをオンボードし、プロンプトで **[はい]** を選択して続行します。

## <a name="create-availability-group"></a>可用性グループを作成する

クラスターを作成またはオンボードした後、Azure portal を使用して可用性グループを作成します。 これを行うには、次のステップに従います。

1. [Azure Portal](https://portal.azure.com) にサインインします。 
1. [SQL 仮想マシン](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) リソースに移動します。 
1. **[設定]** の **[高可用性]** を選択します。 
1. **[+ New Always On availability group]\(+ 新しい Always On 可用性グループ\)** を選択して **[Create availability group]\(可用性グループの作成\)** ページを開きます。

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-availability-group.png" alt-text="[new always on availability group]\(新しい Always On 可用性グループ\) を選択して [Create availability group]\(可用性グループの作成\) ページを開く。":::

1. 可用性グループの名前を入力します。 
1. **[Configure listener]\(リスナーの構成\)** を選択して **[可用性グループのリスナーの構成]** ページを開きます。 

   :::image type="content" source="media/availability-group-az-portal-configure/create-availability-group.png" alt-text="可用性グループの名前を指定してリスナーを構成する":::

1. 値を入力し、既存のロード バランサーを使用するか、 **[新規作成]** を選択して新しいロード バランサーを作成します。  **[適用]** を選択して設定を保存し、リスナーとロード バランサーを作成します。 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-listener.png" alt-text="フォームに値を入力して、新しいリスナーとロード バランサーを作成する":::

1. **[+ Select replica]\(+ レプリカの選択\)** を選択して、 **[Configure availability group replicas]\(可用性グループ レプリカの構成\)** ページを開きます。
1. 可用性グループに追加する仮想マシンを選択し、ビジネス ニーズに最適な可用性グループの設定を選択します。 **[適用]** を選択して設定を保存します。 

   :::image type="content" source="media/availability-group-az-portal-configure/add-replicas.png" alt-text="可用性グループに追加する VM を選択し、ビジネスに適した設定を構成する":::

1. 可用性グループの設定を確認し、 **[適用]** を選択して可用性グループを作成します。 

デプロイの状態は、上部のナビゲーション バーにあるベルのアイコンからアクセスできる **[アクティビティ ログ]** で確認できます。 

  > [!NOTE]
  > Azure portal の **[高可用性]** ページにある **[Synchronization health]\(同期の正常性\)** は、データベースを可用性グループに追加するまでは **[異常]** と表示されます。 


## <a name="add-database-to-availability-group"></a>可用性グループへのデータベースの追加

デプロイが完了したら、データベースを可用性グループに追加します。 以下の手順では SQL Server Management Studio (SSMS) を使用しますが、[Transact-SQL または PowerShell](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) も使用できます。 

SQL Server Management Studio を使用して可用性グループにデータベースを追加するには、次の手順を実行します。

1. リモート デスクトップ接続 (RDP) などの任意の方法を使用して、SQL Server VM のいずれかに接続します。 
1. SQL Server Management Studio (SSMS) を開きます。
1. SQL Server インスタンスに接続します。 
1. **オブジェクト エクスプローラー** で **[Always On 高可用性]** を展開します。
1. **[可用性グループ]** を展開し、可用性グループを右クリックして、 **[データベースの追加...]** を選択します。

   :::image type="content" source="media/availability-group-az-portal-configure/add-database.png" alt-text="オブジェクト エクスプローラーで可用性グループを右クリックし、データベースを追加するよう選択":::

1. プロンプトに従って、可用性グループに追加するデータベースを選択します。 
1. **[OK]** を選択して設定を保存し、データベースを可用性グループに追加します。 
1. データベースを追加したら、**オブジェクト エクスプローラー** を更新して、データベースの状態が `synchronized` であることを確認します。 

データベースを追加した後は、Azure portal で可用性グループの状態を確認できます。 

:::image type="content" source="media/availability-group-az-portal-configure/healthy-availability-group.png" alt-text="データベースが同期された後に Azure portal の [高可用性] ページから可用性グループの状態を確認する":::

## <a name="add-more-vms"></a>VM を追加する

クラスターに SQL Server VM をさらに追加するには、次の手順を実行します。 

1. [Azure Portal](https://portal.azure.com) にサインインします。 
1. [SQL 仮想マシン](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) リソースに移動します。 
1. **[設定]** の **[高可用性]** を選択します。 
1. **[Windows Server フェールオーバー クラスターの構成]** を選択して **[Windows Server フェールオーバー クラスターの構成]** ページを開きます。 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-existing-cluster.png" alt-text="[Windows Server フェールオーバー クラスターの構成] を選択してクラスターに VM を追する。":::

1. **[Windows Server Failover Cluster credentials]\(Windows Server フェールオーバー クラスターの資格情報\)** を展開し、SQL Server サービス、クラスター オペレーター、およびクラスター ブートストラップ アカウントに使用するアカウントを入力します。 
1. クラスターに追加する SQL Server VM を選択します。 
1. **[適用]** を選択します。 

デプロイの状態は、上部のナビゲーション バーにあるベルのアイコンからアクセスできる **[アクティビティ ログ]** で確認できます。 


## <a name="modify-availability-group"></a>可用性グループの変更 


Azure portal の **[高可用性]** ページから、可用性グループの横にある省略記号 (...) を選択することで、可用性グループへの **レプリカの追加**、**リスナーの構成**、および **リスナーの削除** を行うことができます。 

:::image type="content" source="media/availability-group-az-portal-configure/configure-listener.png" alt-text="可用性グループの横にある省略記号を選択し、[レプリカの追加] を選択して、可用性グループにレプリカを追加する。":::

## <a name="remove-cluster"></a>クラスターの削除

クラスターからすべての SQL Server VM を削除して破棄し、SQL IaaS Agent 拡張機能からクラスター メタデータを削除します。 これを行うには、最新バージョンの [Azure CLI](/cli/azure/install-azure-cli) または PowerShell を使用します。 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

まず、クラスターからすべての SQL Server VM を削除します。 これにより、クラスターからノードが物理的に削除され、クラスターが破棄されます。  

```azurecli-interactive
# Remove the VM from the cluster metadata
# example: az sql vm remove-from-group --name SQLVM2 --resource-group SQLVM-RG

az sql vm remove-from-group --name <VM1 name>  --resource-group <resource group name>
az sql vm remove-from-group --name <VM2 name>  --resource-group <resource group name>
```

これらがクラスター内にある VM のすべてであった場合、クラスターは破棄されます。 削除された SQL Server VM とは別に、クラスター内に他の VM がある場合、他の VM は削除されず、クラスターは破棄されません。 

次に、SQL IaaS Agent 拡張機能からクラスターのメタデータを削除します。 

```azurecli-interactive
# Remove the cluster from the SQL VM RP metadata
# example: az sql vm group delete --name Cluster --resource-group SQLVM-RG

az sql vm group delete --name <cluster name> --resource-group <resource group name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

まず、クラスターからすべての SQL Server VM を削除します。 これにより、クラスターからノードが物理的に削除され、クラスターが破棄されます。 

```powershell-interactive
# Remove the SQL VM from the cluster
# example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
#  $sqlvm. SqlVirtualMachineGroup = ""
#  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

$sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
   $sqlvm. SqlVirtualMachineGroup = ""
   
   Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
```

これらがクラスター内にある VM のすべてであった場合、クラスターは破棄されます。 削除された SQL Server VM とは別に、クラスター内に他の VM がある場合、他の VM は削除されず、クラスターは破棄されません。 


次に、SQL IaaS Agent 拡張機能からクラスターのメタデータを削除します。 

```powershell-interactive
# Remove the cluster metadata
# example: Remove-AzSqlVMGroup -ResourceGroupName "SQLVM-RG" -Name "Cluster"

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name>"
```

---

## <a name="troubleshooting"></a>トラブルシューティング

問題が発生した場合は、デプロイ履歴を確認し、一般的なエラーとその解決策を確認することができます。 

### <a name="check-deployment-history"></a>デプロイ履歴を確認する

ポータルを使用してクラスターと可用性グループに加えた変更は、デプロイによって行われます。 デプロイ履歴は、クラスターの作成やオンボード、または可用性グループの作成に関する問題がある際に、より詳細な情報を提供します。

デプロイのログを表示し、デプロイ履歴を確認するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. リソース グループに移動します。
1. **[設定]** で **[デプロイ]** を選択します。
1. デプロイの詳細については、その目的のデプロイを選択してください。 


   :::image type="content" source="media/availability-group-az-portal-configure/failed-deployment.png" alt-text="詳細については、その目的のデプロイを選択してください。" :::

### <a name="common-errors"></a>一般的なエラー

次の一般的なエラーとその解決策を確認します。 

#### <a name="the-account-which-is-used-to-start-up-sql-service-is-not-a-domain-account"></a>SQL サービスを開始するために使用されるアカウントがドメイン アカウントではない

これは、指定された資格情報を使用して、リソース プロバイダーから SQL Server サービスにアクセスできなかったことを示しています。 一般的な解決策をいくつか次に示します。
- ドメイン コントローラーが実行されていることを確認します。
- ポータルで提供された資格情報が、SQL Server サービスの資格情報と一致していることを確認します。 


## <a name="next-steps"></a>次のステップ


可用性グループの詳細については、次を参照してください。

- [可用性グループの概要](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
* [可用性グループの管理](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [可用性グループの監視 &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [可用性グループの Transact-SQL ステートメント](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [可用性グループの PowerShell コマンド](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  

SQL Server VM の詳細については、次を参照してください。 

* [SQL Server VM の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SQL Server VM のリリース ノート](../../database/doc-changes-updates-release-notes.md)
* [SQL Server VM の FAQ](frequently-asked-questions-faq.md)
