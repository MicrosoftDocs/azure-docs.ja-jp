---
title: AG VNN リスナーのロード バランサーの構成
description: 高可用性とディザスター リカバリー (HADR) を実現するために、Azure Load Balancer を構成して、Azure VM 上の SQL Server を使用する可用性グループの仮想ネットワーク名 (VNN) リスナーにトラフィックをルーティングする方法について説明します。
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
ms.date: 06/02/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 2d89759438cb625a0e220af10ab6b287096f6390
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359882"
---
# <a name="configure-load-balancer-for-ag-vnn-listener"></a>AG VNN リスナーのロード バランサーの構成
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure 仮想マシンでは、一度に 1 つのクラスター ノードに設定する必要がある IP アドレスを保持するために、クラスターでロード バランサーが使用されます。 このソリューションでは、Always On 可用性グループ (AG) の仮想ネットワーク名 (VNN) リスナーの IP アドレスが、ロード バランサーによって保持されます。 

この記事では、Azure Load Balancer サービスを使用してロード バランサーを構成する方法について説明します。 高可用性とディザスター リカバリー (HADR) を実現するために、ロード バランサーによって、Azure VM 上の SQL Server を使用する[可用性グループ (AG) リスナー](availability-group-overview.md)にトラフィックがルーティングされます。 

SQL Server 2019 CU8 以降を使用している顧客向けの代替の接続オプションとしては、構成を簡素化し、フェールオーバーを改善するために、代わりに [DNN リスナー](availability-group-vnn-azure-load-balancer-configure.md)を検討してください。  



## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

- Azure Load Balancer が[お客様の HADR ソリューションに適切な接続オプション](hadr-cluster-best-practices.md#connectivity)であると判断済みであること。
- [可用性グループ リスナー](availability-group-overview.md)が構成済みであること。
- 最新バージョンの [PowerShell](/powershell/azure/install-az-ps) をインストール済みであること。 


## <a name="create-load-balancer"></a>ロード バランサーの作成

[Azure portal](https://portal.azure.com) を使用してロード バランサーを作成します。

1. Azure Portal で、仮想マシンが含まれているリソース グループに移動します。

1. **[追加]** を選択します。 Azure Marketplace で「**ロード バランサー**」を検索します。 **[ロード バランサー]** を選択します。

1. **［作成］** を選択します

1. 次の値を使用してロード バランサーを設定します。

   - **サブスクリプション**:Azure サブスクリプション。
   - **[リソース グループ]** :仮想マシンが含まれているリソース グループ。
   - **Name**:ロード バランサーを識別する名前。
   - **[リージョン]** :仮想マシンが含まれている Azure の場所。
   - **[種類]** :パブリックまたはプライベート。 プライベート ロード バランサーには、同じ仮想ネットワーク内からアクセスできます。 プライベート ロード バランサーは、ほとんどの Azure アプリケーションで使用できます。 アプリケーションがインターネット経由で直接 SQL Server にアクセスする必要がある場合は、パブリック ロード バランサーを使用します。
   - **SKU**:Standard。
   - **仮想ネットワーク**:仮想マシンと同じネットワーク。
   - **[IP アドレスの割り当て]** :静的。 
   - **[プライベート IP アドレス]** :クラスター化されたネットワーク リソースに割り当てた IP アドレス。

   次の画像は **[ロード バランサーの作成]** の UI を示しています。

   ![ロードバランサーを設定する](./media/failover-cluster-instance-premium-file-share-manually-configure/30-load-balancer-create.png)
   

## <a name="configure-backend-pool"></a>バックエンド プールを構成する

1. 仮想マシンが含まれている Azure リソース グループに戻り、新しいロード バランサーを探します。 リソース グループの表示を更新することが必要な場合もあります。 ロード バランサーを選択します。

1. **[バックエンド プール]** を選択し、 **[追加]** を選択します。

1. VM を含む可用性セットにバックエンド プールを関連付けます。

1. **[ターゲット ネットワーク IP 構成]** で、 **[仮想マシン]** を選択し、クラスター ノードとして参加する仮想マシンを選びます。 必ず、FCI または可用性グループをホストするすべての仮想マシンを含めます。

1. **[OK]** を選択して、バックエンド プールを作成します。

## <a name="configure-health-probe"></a>正常性プローブを構成する

1. [ロード バランサー] ペインで **[正常性プローブ]** を選択します。

1. **[追加]** を選択します。

1. **[正常性プローブの追加]** ペインで、<span id="probe"></span> 次の正常性プローブのパラメーターを設定します。

   - **Name**:正常性プローブの名前。
   - **プロトコル**:TCP
   - **ポート**:[VM を準備したときに](failover-cluster-instance-prepare-vm.md#uninstall-sql-server-1)正常性プローブ用にファイアウォールで作成したポート。 この記事の例では、TCP ポート `59999` を使用します。
   - **間隔**: 5 秒
   - **[異常のしきい値]** : 連続エラー数 2。

1. **[OK]** を選択します。

## <a name="set-load-balancing-rules"></a>負荷分散規則を設定する

1. [ロード バランサー] ペインで、 **[負荷分散規則]** を選択します。

1. **[追加]** を選択します。

1. 次のように負荷分散規則のパラメーターを設定します。

   - **Name**:負荷分散規則の名前。
   - **[フロントエンド IP アドレス]** : SQL Server FCI または AG リスナーのクラスター化されたネットワーク リソースの IP アドレス。
   - **ポート**:SQL Server の TCP ポート。 既定のインスタンス ポートは 1433 です。
   - **[バックエンド ポート]** : **[フローティング IP (ダイレクト サーバー リターン)]** を有効にしたときの **[ポート]** の値と同じポート。
   - **[バックエンド プール]** : 先ほど構成したバックエンド プール名。
   - **[正常性プローブ]** : 先ほど構成した正常性プローブ。
   - **[セッション永続化]** : [なし] :
   - **[アイドル タイムアウト (分)]** : 4.
   - **[フローティング IP]\(ダイレクト サーバー リターン\)** : 有効。

1. **[OK]** を選択します。

## <a name="configure-cluster-probe"></a>クラスターのプローブを構成する

PowerShell でクラスターのプローブ ポート パラメーターを設定します。

クラスターのプローブ ポート パラメーターを設定するには、使用環境の値を使用して、次のスクリプトで変数を更新します。 スクリプトから山かっこ (`<` および `>`) を削除します。

```powershell
$ClusterNetworkName = "<Cluster Network Name>"
$IPResourceName = "<SQL Server FCI / AG Listener IP Address Resource Name>" 
$ILBIP = "<n.n.n.n>" 
[int]$ProbePort = <nnnnn>

Import-Module FailoverClusters

Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
```

次の表では、更新する必要がある値について説明します。


|**Value**|**説明**|
|---------|---------|
|`Cluster Network Name`| ネットワークの Windows Server フェールオーバー クラスターの名前。 **[フェールオーバー クラスター マネージャー]**  >  **[ネットワーク]** で、ネットワークを右クリックして **[プロパティ]** を選択します。 正しい値は **[全般]** タブの **[名前]** にあります。|
|`AG listener IP Address Resource Name`|SQL Server FCI または AG リスナーの IP アドレスのリソース名。 **[フェールオーバー クラスター マネージャー]**  >  **[Roles]\(ロール\)** で、SQL Server FCI ロールの **[サーバー名]** の下にある IP アドレス リソースを右クリックして **[プロパティ]** を選択します。 正しい値は **[全般]** タブの **[名前]** にあります。|
|`ILBIP`|内部ロード バランサー (ILB) の IP アドレス。 このアドレスは、Azure portal で ILB のフロントエンド アドレスとして構成されます。 これは、SQL Server FCI の IP アドレスでもあります。 **[フェールオーバー クラスター マネージャー]** の `<AG listener IP Address Resource Name>` がある同じプロパティ ページで見つけることができます。|
|`nnnnn`|ロード バランサーの正常性プローブで構成したプローブ ポート。 未使用の TCP ポートが有効です。|
|"SubnetMask"| クラスター パラメーターのサブネット マスク。 TCP IP のブロードキャスト アドレス `255.255.255.255` である必要があります。| 


クラスターのプローブを設定したら、PowerShell ですべてのクラスター パラメーターを確認できます。 このスクリプトを実行します。

```powershell
Get-ClusterResource $IPResourceName | Get-ClusterParameter
```


## <a name="test-failover"></a>[テスト フェールオーバー]


クラスター化されたリソースのフェールオーバーをテストして、クラスターの機能を検証します。 


次の手順を実行します。

1. [SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms) を開き、可用性グループ リスナーに接続します。 
1. **オブジェクト エクスプローラー** で **[Always On 可用性グループ]** を展開します。 
1. 可用性グループを右クリックして、 **[フェールオーバー]** を選択します。 
1. ウィザードの指示に従って、可用性グループをセカンダリ レプリカにフェールオーバーします。 

レプリカのロールが切り替わり、両方が同期された場合、フェールオーバーは成功です。 


## <a name="test-connectivity"></a>接続をテストする

接続をテストするには、同じ仮想ネットワーク内の別の仮想マシンにサインインします。 **SQL Server Management Studio** を開き、可用性グループ リスナーに接続します。

>[!NOTE]
>必要に応じて、[SQL Server Management Studio をダウンロード](/sql/ssms/download-sql-server-management-studio-ssms)できます。

## <a name="next-steps"></a>次のステップ

Azure での SQL Server の HADR 機能について詳しくは、[可用性グループ](availability-group-overview.md)と[フェールオーバー クラスター インスタンス](failover-cluster-instance-overview.md)に関する記事をご覧ください。 また、高可用性とディザスター リカバリー用に環境を構成するための[ベスト プラクティス](hadr-cluster-best-practices.md)を学習することもできます。 



