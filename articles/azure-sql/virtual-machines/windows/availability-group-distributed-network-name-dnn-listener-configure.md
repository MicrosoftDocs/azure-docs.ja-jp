---
title: 可用性グループの DNN リスナーの構成
description: 分散ネットワーク名 (DNN) リスナーを構成して、仮想ネットワーク名 (VNN) リスナーを置き換え、Azure VM 上の SQL Server で Always On 可用性グループにトラフィックをルーティングする方法について説明します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 07ce01304f27ded4e0a566777fcf7027f7a15e4b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359440"
---
# <a name="configure-a-dnn-listener-for-an-availability-group"></a>可用性グループの DNN リスナーの構成
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure VM 上の SQL Server を使用すると、分散ネットワーク名 (DNN) によって、クラスター化された適切なリソースにトラフィックがルーティングされます。 これにより、仮想ネットワーク名 (VNN) リスナーよりも簡単に Always On 可用性グループ (AG) に接続する方法が提供されます。Azure Load Balancer は必要ありません。 

この記事では、高可用性とディザスター リカバリー (HADR) のために、DNN リスナーを構成して、VNN リスナーを置き換え、Azure VM 上の SQL Server で可用性グループにトラフィックをルーティングする方法について説明します。 

DNN リスナー機能は、現在、Windows Server 2016 以降上の SQL Server 2019 CU8 以降でのみ使用できます。 

代替の接続オプションとしては、代わりに [VNN リスナーと Azure Load Balancer](availability-group-vnn-azure-load-balancer-configure.md) を検討してください。 

## <a name="overview"></a>概要

分散ネットワーク名 (DNN) リスナーは、[SQL Server VM 上の Always On 可用性グループ](availability-group-overview.md)と共に使用した場合、従来の仮想ネットワーク名 (VNN) 可用性グループ リスナーに取って代わります。 これにより、トラフィックのルーティングに Azure Load Balancer が不要になり、デプロイと保守が簡素化され、フェールオーバーが向上します。 

DNN リスナーを既存の VNN リスナーの代わりに使用するか、または既存の VNN リスナーと組み合わせて使用します。後者の場合は、可用性グループに 2 つの異なる接続ポイント (VNN リスナー名 (既定以外の場合はポートも) を使用するものと、DNN リスナー名およびポートを使用するもの) が存在します。 

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

- Windows Server 2016 以降上の SQL Server 2019 CU8 以降
- 分散ネットワーク名が[お客様の HADR ソリューションに適切な接続オプション](hadr-cluster-best-practices.md#connectivity)であると判断済みであること。
- [Always On 可用性グループ](availability-group-overview.md)を構成済みであること。 
- 最新バージョンの [PowerShell](/powershell/azure/install-az-ps) をインストール済みであること。 


## <a name="create-script"></a>スクリプトの作成

PowerShell を使用して分散ネットワーク名 (DNN) リソースを作成し、可用性グループに関連付けます。 

これを行うには、次の手順に従います。 

1. メモ帳などのテキスト エディターを開きます。 
1. 次のスクリプトをコピーして貼り付けます。 

   ```powershell
   param (
      [Parameter(Mandatory=$true)][string]$Ag,
      [Parameter(Mandatory=$true)][string]$Dns,
      [Parameter(Mandatory=$true)][string]$Port
   )
   
   Write-Host "Add a DNN listener for availability group $Ag with DNS name $Dns and port $Port"
   
   $ErrorActionPreference = "Stop"
   
   # create the DNN resource with the port as the resource name
   Add-ClusterResource -Name $Port -ResourceType "Distributed Network Name" -Group $Ag 
   
   # set the DNS name of the DNN resource
   Get-ClusterResource -Name $Port | Set-ClusterParameter -Name DnsName -Value $Dns 
   
   # start the DNN resource
   Start-ClusterResource -Name $Port
   
   
   $Dep = Get-ClusterResourceDependency -Resource $Ag
   if ( $Dep.DependencyExpression -match '\s*\((.*)\)\s*' )
   {
   $DepStr = "$($Matches.1) or [$Port]"
   }
   else
   {
   $DepStr = "[$Port]"
   }
   
   Write-Host "$DepStr"
   
   # add the Dependency from availability group resource to the DNN resource
   Set-ClusterResourceDependency -Resource $Ag -Dependency "$DepStr"
   
   
   #bounce the AG resource
   Stop-ClusterResource -Name $Ag
   Start-ClusterResource -Name $Ag
   ```

1. スクリプトを `add_dnn_listener.ps1` などの `.ps1` ファイルとして保存します。 


## <a name="execute-script"></a>スクリプトの実行

DNN リスナーを作成するには、可用性グループ名、リスナー名、およびポートのパラメーターを渡すスクリプトを実行します。 

たとえば、可用性グループ名 `ag1`、リスナー名 `dnnlsnr`、リスナー ポート `6789` を想定した場合、次の手順を実行します。 

1. コマンド プロンプトや PowerShell などのコマンドライン インターフェイス ツールを開きます。 
1. `.ps1` スクリプトを保存した場所 (C:\Documents など) に移動します。 
1. スクリプト ```add_dnn_listener.ps1 <ag name> <listener-name> <listener port>``` を実行します。 次に例を示します。 

   ```console
   c:\Documents> add_dnn_listener.ps1 ag1 dnnlsnr 6789
   ```

## <a name="verify-listener"></a>リスナーの確認

DNN リスナーが正常に作成されたことを確認するには、SQL Server Management Studio または Transact-SQL を使用します。 

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

[SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) で **[可用性グループ リスナー]** を展開して、DNN リスナーを表示します。 

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-in-ssms.png" alt-text="SQL Server Management Studio (SSMS) の [可用性グループ リスナー] で DNN リスナーを表示する":::

### <a name="transact-sql"></a>Transact-SQL

Transact-SQL を使用して、DNN リスナーの状態を表示します。 

```sql
SELECT * FROM SYS.AVAILABILITY_GROUP_LISTENERS
```

`is_distributed_network_name` の値 `1` は、リスナーが分散ネットワーク名 (DNN) リスナーであることを示します。 

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-tsql.png" alt-text="sys.availability_group_listeners を使用して、is_distributed_network_name の値が 1 である DNN リスナーを識別する":::


## <a name="update-connection-string"></a>接続文字列を更新する

アプリケーションの接続文字列を更新して、DNN リスナーに接続されるようにします。 フェールオーバー後の速やかな接続を確保するために、`MultiSubnetFailover=True` を接続文字列に追加します (SQL クライアントでサポートされている場合)。 

## <a name="test-failover"></a>[テスト フェールオーバー]

可用性グループのフェールオーバーをテストして、機能を確認します。 

フェールオーバーをテストするには、次の手順に従います。 

1. [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) を使用して、DNN リスナーまたはいずれかのレプリカに接続します。 
1. **オブジェクト エクスプローラー** で **[Always On 可用性グループ]** を展開します。 
1. 可用性グループを右クリックし、 **[フェールオーバー]** を選択して、**フェールオーバー ウィザード** を開きます。 
1. プロンプトに従ってフェールオーバー ターゲットを選択し、可用性グループをセカンダリ レプリカにフェールオーバーします。 
1. 新しいプライマリ レプリカで、データベースが同期状態であることを確認します。 
1. (省略可能) 元のプライマリ レプリカまたは別のセカンダ リレプリカにフェールバックします。 

## <a name="test-connectivity"></a>接続をテストする

次の手順に従って、DNN リスナーへの接続をテストします。

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) を開きます。
1. DNN リスナーに接続します。 
1. 新しいクエリ ウィンドウを開き、`SELECT @@SERVERNAME` を実行して、接続しているレプリカを確認します。 
1. 可用性グループを別のレプリカにフェールオーバーします。
1. 十分な時間が経過したら、`SELECT @@SERVERNAME` を実行して、可用性グループが別のレプリカでホストされていることを確認します。 


## <a name="limitations"></a>制限事項

- 現時点では、可用性グループの DNN リスナーは、Windows Server 2016 以降上の SQL Server 2019 CU8 以降でのみサポートされています。 
- DNN を使用してその他の SQL Server 機能と可用性グループを操作する場合は、さらなる考慮事項が生じることがあります。 詳細については、[AG と DNN の相互運用性](availability-group-dnn-interoperability.md)に関する記事をご覧ください。 

## <a name="next-steps"></a>次のステップ

Azure での SQL Server の HADR 機能について詳しくは、[可用性グループ](availability-group-overview.md)と[フェールオーバー クラスター インスタンス](failover-cluster-instance-overview.md)に関する記事をご覧ください。 また、高可用性とディザスター リカバリー用に環境を構成するための[ベスト プラクティス](hadr-cluster-best-practices.md)を学習することもできます。 


