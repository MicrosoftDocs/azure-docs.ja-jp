---
title: AlwaysOn 可用性グループの外部リスナーの構成 | Microsoft Docs
description: このチュートリアルでは、関連付けられているクラウド サービスのパブリック仮想 IP アドレスを使用して外部からアクセスできる、AlwaysOn 可用性グループ リスナーを Azure で作成する手順について説明します。
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a2453032-94ab-4775-b976-c74d24716728
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: 78881830d4e558daaad6e1929b30287e2731fb1b
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100411"
---
# <a name="configure-an-external-listener-for-always-on-availability-groups-in-azure"></a>Azure での AlwaysOn 可用性グループの外部リスナーの構成
> [!div class="op_single_selector"]
> * [内部リスナー](../classic/ps-sql-int-listener.md)
> * [外部リスナー](../classic/ps-sql-ext-listener.md)
> 
> 

このトピックでは、インターネット上で外部からアクセス可能な、AlwaysOn 可用性グループのリスナーを構成する方法について説明します。 これは、クラウド サービスの **パブリック仮想 IP (VIP)** アドレスをリスナーに関連付けることにより可能になります。

> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、2 種類のデプロイ モデルがあります。[Resource Manager とクラシック](../../../azure-resource-manager/resource-manager-deployment-model.md)です。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイメントでは、リソース マネージャー モデルを使用することをお勧めします。

可用性グループには、オンプレミスのみ、Azure のみ、またはオンプレミスと Azure の両方にまたがるハイブリッド構成のレプリカを含めることができます。 Azure レプリカは、同じリージョン内に配置することも、複数の仮想ネットワーク (VNet) を使用して複数のリージョンに配置することもできます。 後述の手順では、既に[可用性グループは構成している](../classic/portal-sql-alwayson-availability-groups.md)ものの、リスナーは構成していないと仮定しています。

## <a name="guidelines-and-limitations-for-external-listeners"></a>外部リスナーのガイドラインと制限事項
クラウド サービスのパブリック VIP アドレスを使用してデプロイしている場合は、Azure での可用性グループ リスナーに関する次のガイドラインを確認してください。

* 可用性グループ リスナーは、Windows Server 2008 R2、Windows Server 2012、および Windows Server 2012 R2 でサポートされます。
* クライアント アプリケーションは、可用性グループ VM が含まれるクラウド サービスとは異なるクラウド サービスに存在する必要があります。 Azure では、同じクラウド サービスに存在するクライアントとサーバーによる Direct Server Return はサポートされません。
* 既定では、この記事の手順により、クラウド サービスの仮想 IP (VIP) アドレスを使用するリスナーが 1 つ構成されます。 ただし、クラウド サービスに対して複数の VIP アドレスを予約して作成することができます。 これにより、この記事の手順を使用して、別の VIP にそれぞれ関連付けられている複数のリスナーを作成することができます。 複数の VIP アドレスを作成する方法の詳細については、「 [クラウド サービスごとの複数の VIP](../../../load-balancer/load-balancer-multivip.md)」を参照してください。
* ハイブリッド環境でのリスナーを作成する場合、オンプレミスのネットワークは、Azure 仮想ネットワークを使用したサイト間 VPN への接続に加えて、パブリック インターネットへの接続が必要です。 Azure のサブネットでは、可用性グループ リスナーは個々 のクラウド サービスのパブリック IP アドレスによってのみ到達可能です。
* 内部ロード バランサー (ILB) を使用する内部リスナーも含まれるクラウド サービスに外部リスナーを作成することはできません。

## <a name="determine-the-accessibility-of-the-listener"></a>リスナーのアクセシビリティを決定する
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

この記事では、 **外部負荷分散**を使用するリスナーの作成を中心に説明します。 仮想ネットワーク専用のリスナーが必要な場合は、この記事の、[ILB を使用するリスナー](../classic/ps-sql-int-listener.md)を設定するための手順を説明するバージョンをご覧ください。

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Direct Server Return を使用して負荷分散 VM エンドポイントを作成する
外部負荷分散では、VM をホストするクラウド サービスのパブリック仮想 IP アドレスを使用します。 そのため、この場合はロード バランサーを作成または構成する必要はありません。

Azure レプリカをホストする各 VM に負荷分散されたエンドポイントを作成する必要があります。 複数のリージョンでレプリカがある場合は、そのリージョンの各レプリカが同じ VNet 内の同じクラウド サービスにある必要があります。 複数の Azure リージョンにまたがる可用性グループ レプリカを作成するには、複数の VNet を構成する必要があります。 クロス VNet 接続の構成については、[VNet 間の接続の構成](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)に関する記事をご覧ください。

1. Azure ポータルで、レプリカをホストする各 VM に移動し、詳細を表示します。
2. 各 VM の **[エンドポイント]** タブをクリックします。
3. 使用しようとしているリスナー エンドポイントの **[名前]** と **[パブリック ポート]** が未使用であることを確認します。 次の例では、名前が "MyEndpoint" で、ポートが “1433” とします。
4. ローカル クライアントに、 [最新の PowerShell モジュール](https://azure.microsoft.com/downloads/)をダウンロードしてインストールします。
5. **Azure PowerShell**を起動します。 Azure 管理モジュールが読み込まれた状態で新しい PowerShell セッションが開きます。
6. **Get-AzurePublishSettingsFile**を実行します。 このコマンドレットにより、ブラウザーで発行設定ファイルをローカル ディレクトリにダウンロードするよう指示されます。 Azure サブスクリプションのログイン資格情報の入力を求められる場合があります。
7. ダウンロードした発行設定ファイルのパスを指定して、 **Import-AzurePublishSettingsFile** コマンドを実行します。
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    発行設定ファイルがインポートされたら、PowerShell セッションで、Azure サブスクリプションを管理できます。
    
1. 次の PowerShell スクリプトをテキスト エディターにコピーし、環境に合わせて変数の値を設定します (一部のパラメーターには既定値が指定されています)。 可用性グループが複数の Azure リージョンにまたがっている場合、各データセンターにおいて、そのデータセンターに存在するクラウド サービスおよびノードごとにスクリプトを 1 回ずつ実行する必要があることに注意してください。
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

2. 変数の設定後、スクリプトを、テキスト エディターからそれを実行する Azure PowerShell セッションにコピーします。 プロンプトにまだ >> が表示される場合、スクリプトの実行が確実に開始されるようにするため、Enter キーを再度押します。

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>KB2854082 がインストールされていることを確認する (必要に応じて)
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>可用性グループ ノードでファイアウォール ポートを開く
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>可用性グループ リスナーを作成する

可用性グループ リスナーを 2 つの手順で作成します。 まず、クライアント アクセス ポイントのクラスター リソースを作成し、依存関係を構成します。 次に、PowerShell を使用してクラスター リソースを構成します。

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>クライアント アクセス ポイントを作成してクラスターの依存関係を構成する
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>PowerShell でクラスター リソースを構成する
1. 外部負荷分散の場合、レプリカが含まれるクラウド サービスのパブリック仮想 IP アドレスを取得する必要があります。 Azure Portal にログインします。 可用性グループの VM が含まれるクラウド サービスに移動します。 **[ダッシュボード]** ビューを開きます。
2. **[パブリック仮想 IP (VIP) アドレス]** に表示されているアドレスをメモします。 ソリューションが複数の VNet にまたがっている場合は、レプリカをホストする VM が含まれるクラウド サービスごとにこの手順を繰り返します。
3. VM のいずれかで、次の PowerShell スクリプトをテキスト エディターにコピーし、変数を先ほどメモした値に設定します。
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. 変数の設定後、管理者特権で Windows PowerShell ウィンドウを開いて、スクリプトをテキスト エディターからコピーし、それを実行する Azure PowerShell セッションに貼り付けます。 プロンプトにまだ >> が表示される場合、スクリプトの実行が確実に開始されるようにするため、Enter キーを再度押します。
5. 各 VM で、これを繰り返します。 このスクリプトでは、クラウド サービスの IP アドレスを使用して IP アドレス リソースを構成し、プローブ ポートなどの他のパラメーターを設定します。 IP アドレス リソースがオンラインになると、プローブ ポートにおいて、このチュートリアルで先ほど作成した負荷分散エンドポイントからのポーリングに応答できます。

## <a name="bring-the-listener-online"></a>リスナーをオンラインにする
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>フォローアップ項目
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>可用性グループ リスナー(同一 VNet 内) をテストする
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>可用性グループ リスナー(インターネット経由) をテストする
仮想ネットワークの外部からリスナーにアクセスするには、同じ VNet 内のみアクセス可能な ILB ではなく、外部/公開負荷分散 (このトピックで説明) を使用している必要があります。 接続文字列で、クラウド サービス名を指定します。 たとえば、クラウド サービスの名前が *mycloudservice*の場合、sqlcmd ステートメントは次のようになります。

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

呼び出し元はインターネット経由で windows 認証を使用できないため、前の例とは異なり SQL 認証を使用する必要があります。 詳細については、[Azure VM での AlwaysOn 可用性グループ: クライアント接続のシナリオ](https://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx)に関するページを参照してください。 SQL 認証を使用する場合は、両方のレプリカで必ず同じログインを作成します。 可用性グループのログインに関するトラブルシューティングの詳細については、「 [How to map logins or use contained SQL database user to connect to other replicas and map to availability databases (ログインをマップするか、または 包含 SQL データベース ユーザーを使用して他のレプリカに接続し、可用性データベースにマップする方法)](https://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx)」を参照してください。

AlwaysOn レプリカが別のサブネットにある場合、クライアントは接続文字列で **MultisubnetFailover=True** を指定する必要があります。 これにより、別のサブネット内のレプリカへのパラレル接続が試行されます。 このシナリオには、AlwaysOn 可用性グループのリージョンをまたがるデプロイが含まれていることに注意してください。

## <a name="next-steps"></a>次の手順
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]

