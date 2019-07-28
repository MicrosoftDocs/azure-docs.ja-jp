---
title: Azure で AlwaysOn 可用性グループの ILB リスナーを構成する | Microsoft Docs
description: このチュートリアルでは、クラシック デプロイ モデルを使用して作成されたリソースを使用し、内部ロード バランサーを使用して Azure で AlwaysOn 可用性グループ リスナーを作成します。
services: virtual-machines-windows
documentationcenter: na
author: mikeraymsft
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 291288a0-740b-4cfa-af62-053218beba77
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.openlocfilehash: 0e6a52ea2fdd05546a4da9f8cd1165b41ed27944
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "62097742"
---
# <a name="configure-an-ilb-listener-for-always-on-availability-groups-in-azure"></a>Azure で AlwaysOn 可用性グループの ILB リスナーを構成する
> [!div class="op_single_selector"]
> * [内部リスナー](../classic/ps-sql-int-listener.md)
> * [外部リスナー](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>概要

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、2 種類のデプロイ モデルがあります。[Azure Resource Manager とクラシックです](../../../azure-resource-manager/resource-manager-deployment-model.md)。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 ほとんどの新しいデプロイでは、Resource Manager モデルを使用することをお勧めします。

Resource Manager モデルで AlwaysOn 可用性グループのリスナーを構成する方法については、「[Azure の AlwaysOn 可用性グループに使用する内部ロード バランサーの構成](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)」をご覧ください。

可用性グループには、オンプレミスのみ、Azure のみ、またはオンプレミスと Azure の両方にまたがるハイブリッド構成のレプリカを含めることができます。 Azure レプリカは、同じリージョン内に配置することも、複数の仮想ネットワークを使用して複数のリージョンに配置することもできます。 この記事の手順では、既に[可用性グループは構成している](../classic/portal-sql-alwayson-availability-groups.md)ものの、リスナーは構成していないと仮定しています。

## <a name="guidelines-and-limitations-for-internal-listeners"></a>内部リスナーのガイドラインと制限事項
Azure での内部ロード バランサー (ILB) と可用性グループ リスナーの使用には、次のガイドラインが適用されます。

* 可用性グループ リスナーは、Windows Server 2008 R2、Windows Server 2012、および Windows Server 2012 R2 でサポートされます。
* 内部可用性グループ リスナーは、クラウド サービスごとに 1 つのみサポートされます。これは、リスナーが ILB に対して構成されており、クラウド サービスごとに 1 つの ILB のみが存在するためです。 ただし、複数の外部リスナーを作成することができます。 詳しくは、「[Azure での AlwaysOn 可用性グループの外部リスナーの構成](../classic/ps-sql-ext-listener.md)」をご覧ください。

## <a name="determine-the-accessibility-of-the-listener"></a>リスナーのアクセシビリティを決定する
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

この記事では、ILB を使用するリスナーの作成を中心に説明します。 パブリックまたは外部リスナーが必要な場合は、この記事の、[外部リスナー](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)の設定について説明するバージョンをご覧ください。

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Direct Server Return を使用して負荷分散 VM エンドポイントを作成する
最初に、このセクションで後で説明するスクリプトを実行して、ILB を作成します。

Azure レプリカをホストする VM ごとに負荷分散されたエンドポイントを作成します。 複数のリージョンにレプリカがある場合は、そのリージョンの各レプリカが同じ Azure 仮想ネットワーク内の同じクラウド サービスにある必要があります。 複数の Azure リージョンにまたがる可用性グループ レプリカを作成するには、複数の仮想ネットワークを構成する必要があります。 仮想ネットワーク接続間での構成の詳細については、[仮想ネットワーク接続に対する仮想ネットワークの構成](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)に関するページを参照してください。

1. Azure ポータルで、レプリカをホストする各 VM に移動して、詳細を表示します。

2. 各 VM の **[エンドポイント]** タブをクリックします。

3. 使用しようとしているリスナー エンドポイントの **[名前]** と **[パブリック ポート]** が未使用であることを確認します。 このセクションの例では、名前は *MyEndpoint* で、ポートは *1433* です。

4. ローカル クライアントに、[最新の PowerShell モジュール](https://azure.microsoft.com/downloads/)をダウンロードしてインストールします。

5. Azure PowerShell を開始します。  
    Azure 管理モジュールが読み込まれた状態で新しい PowerShell セッションが開きます。

6. `Get-AzurePublishSettingsFile` を実行します。 このコマンドレットにより、ブラウザーで発行設定ファイルをローカル ディレクトリにダウンロードするよう指示されます。 Azure サブスクリプションのサインイン資格情報の入力を求められる場合があります。

7. 次の `Import-AzurePublishSettingsFile` コマンドを、ダウンロードした発行設定ファイルのパスを指定して実行します。

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    発行設定ファイルがインポートされたら、PowerShell セッションで、Azure サブスクリプションを管理できます。

8. *ILB* の場合は、静的 IP アドレスを割り当てます。 次のコマンドを実行して、現在の仮想ネットワーク構成を確認します。

        (Get-AzureVNetConfig).XMLConfiguration
9. レプリカをホストする VM が含まれるサブネットの *サブネット* 名をメモします。 この名前は、スクリプトの $SubnetName パラメーターで使用します。

10. レプリカをホストする VM が含まれるサブネットの *VirtualNetworkSite* 名と開始する *AddressPrefix* をメモします。 両方の値を `Test-AzureStaticVNetIP` コマンドに渡し、*AvailableAddresses* を確認することによって、使用可能な IP アドレスを探します。 たとえば、仮想ネットワークの名前が *MyVNet* であり、サブネット アドレスの範囲が *172.16.0.128* から始まる場合、次のコマンドを実行すると、使用可能なアドレスのリストが表示されます。

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
11. 使用可能なアドレスのいずれかを選択し、次の手順に含まれるスクリプトの $ILBStaticIP パラメーターで使用します。

12. 次の PowerShell スクリプトをテキスト エディターにコピーし、環境に合わせて変数の値を設定します。 いくつかのパラメーターには、既定値が指定されています。  

    アフィニティ グループを使用している既存のデプロイでは、ILB を追加できません。 ILB の要件の詳細については、「[内部ロード バランサーの概要](../../../load-balancer/load-balancer-internal-overview.md)」を参照してください。

    また、可用性グループが複数の Azure リージョンにまたがっている場合、各データセンターにおいて、そのデータセンターに存在するクラウド サービスおよびノードごとにスクリプトを 1 回ずつ実行する必要があります。

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
        $SubnetName = "<MySubnetName>" # subnet name that the replicas use in the virtual network
        $ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
        $ILBName = "AGListenerLB" # customize the ILB name or use this default value

        # Create the ILB
        Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP

        # Configure a load-balanced endpoint for each node in $AGNodes by using ILB
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
        }

13. 変数を設定したら、スクリプトを、テキスト エディターからそれを実行する PowerShell セッションにコピーします。 プロンプトにまだ **>>** が表示される場合、スクリプトの実行が確実に開始されるようにするため、Enter キーをもう一度押します。

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>KB2854082 がインストールされていることを確認する (必要に応じて)
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>可用性グループ ノードでファイアウォール ポートを開く
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>可用性グループ リスナーを作成する

可用性グループ リスナーを 2 つの手順で作成します。 まず、クライアント アクセス ポイントのクラスター リソースを作成し、依存関係を構成します。 次に、PowerShell でクラスター リソースを構成します。

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>クライアント アクセス ポイントを作成してクラスターの依存関係を構成する
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>PowerShell でクラスター リソースを構成する
1. ILB の場合、先ほど作成した ILB の IP アドレスを使用する必要があります。 PowerShell でこの IP アドレスを取得するには、次のスクリプトを使用します。

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

2. VM のいずれかで、ご利用のオペレーティング システム用の PowerShell スクリプトをテキスト エディターにコピーし、変数を先ほどメモした値に設定します。

    Windows Server 2012 以降の場合、次のスクリプトを使用します。

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

    Windows Server 2008 R2 の場合、次のスクリプトを使用します。

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

3. 変数を設定したら、管理者特権で Windows PowerShell ウィンドウを開き、スクリプトをテキスト エディターからそれを実行する Azure PowerShell セッションに貼り付けます。 プロンプトにまだ **>>** が表示される場合、スクリプトの実行が確実に開始されるようにするため、Enter キーをもう一度押します。

4. VM ごとに以上の手順を繰り返します。  
    このスクリプトでは、クラウド サービスの IP アドレスを使用して IP アドレス リソースを構成し、プローブ ポートなどの他のパラメーターを設定します。 IP アドレス リソースがオンラインになると、プローブ ポートにおいて、先ほど作成した負荷分散エンドポイントからのポーリングに応答できます。

## <a name="bring-the-listener-online"></a>リスナーをオンラインにする
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>フォローアップ項目
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>可用性グループ リスナー(同一仮想ネットワーク内) をテストする
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>次の手順
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
