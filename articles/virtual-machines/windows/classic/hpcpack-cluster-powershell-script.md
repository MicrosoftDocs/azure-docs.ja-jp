---
title: "PowerShell スクリプトで Windows HPC クラスターをデプロイする | Microsoft Docs"
description: "PowerShell スクリプトを実行し、Azure 仮想マシンで Windows HPC Pack 2012 R2 クラスターをデプロイします。"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: 286b2be8-2533-40df-b02a-26156b1f1133
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 85b125ab19671b61d2541af6378c95feb88bf952
ms.lasthandoff: 03/27/2017


---
# <a name="create-a-windows-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>HPC Pack IaaS デプロイ スクリプトを使用し、Windows VM でハイ パフォーマンス コンピューティング (HPC) クラスターを作成する
HPC Pack IaaS デプロイ PowerShell スクリプトを実行し、Windows ワークロード用に完全な HPC Pack 2012 R2 クラスターを Azure 仮想マシンにデプロイします。 このクラスターは、Windows Server と Microsoft HPC Pack を実行する Active Directory に参加するヘッド ノードと、別途指定した Windows コンピューティング リソースとから成ります。 Linux ワークロード用に Azure で HPC Pack クラスターをデプロイする必要がある場合は、「 [HPC Pack IaaS デプロイ スクリプトを使用し、Linux VM でハイ パフォーマンス コンピューティング (HPC) クラスターを作成する](../../linux/classic/hpcpack-cluster-powershell-script.md)」をご覧ください。 Azure リソース マネージャーのテンプレートを使用して HPC Pack クラスターをデプロイすることもできます。 例については、「[Create an HPC cluster](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/)」 (HPC クラスターを作成する) および「[Create an HPC cluster with custom compute node image](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/)」 (カスタム コンピューティング ノード イメージを使用して HPC クラスターを作成する) を参照してください。

> [!IMPORTANT] 
> この記事で説明する PowerShell スクリプトでは、クラシック デプロイメント モデルを使用して Azure で Microsoft HPC Pack 2012 R2 クラスターを作成します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。
> また、この記事で説明するスクリプトは HPC Pack 2016 をサポートしていません。

[!INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-files"></a>サンプル構成ファイル
次の例のサブスクリプション ID (サブスクリプション名)、アカウント名、サービス名には、実際の値を使用してください。

### <a name="example-1"></a>例 1
次の構成ファイルでは、ローカル データベースを持つヘッド ノードを 1 つと Windows Server 2012 R2 オペレーティング システムを実行しているコンピューティング ノードを 5 つ含む HPC Pack クラスターがデプロイされます。 すべてのクラウド サービスは米国西部の場所に直接作成されます。 ヘッド ノードはドメイン フォレストのドメイン コントローラーとして機能します。

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%1000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>5</NodeCount>
    <OSVersion>WindowsServer2012R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### <a name="example-2"></a>例 2
次の構成ファイルでは、既存のドメイン フォレストで HPC Pack クラスターが展開されます。 このクラスターにはローカル データベースを持つヘッド ノードが 1 つあり、BGInfo VM 拡張機能が適用されたコンピューティング ノードが 12 あります。
Windows 更新プログラムの自動インストールはドメイン フォレストのすべての VM で無効です。 すべてのクラウド サービスは東アジアの場所に直接作成されます。 コンピューティング ノードは、3 つのクラウド サービスと 3 つのストレージ アカウント (*MyHPCCNService01* と *mycnstorage01* の *MyHPCCN-0001* ～ *MyHPCCN-0005*、*MyHPCCNService02* と *mycnstorage02* の *MyHPCCN-0006* ～ *MyHPCCN0010*、*MyHPCCNService03* と *mycnstorage03* の *MyHPCCN-0011* ～ *MyHPCCN-0012*) で作成されます。 コンピューティング ノードはコンピューティング ノードからキャプチャされた既存のプライベート イメージから作成されます。 自動拡大縮小サービスは既定の拡大縮小間隔で有効になっています。

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
      </DomainController>
     <NoWindowsAutoUpdate />
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
  </Certificates>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0001%</VMNamePattern>
<ServiceNamePattern>MyHPCCNService%01%</ServiceNamePattern>
<MaxNodeCountPerService>5</MaxNodeCountPerService>
<StorageAccountNamePattern>mycnstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>12</NodeCount>
    <ImageName HPCPackInstalled=”true”>MyHPCComputeNodeImage</ImageName>
    <VMExtensions>
       <VMExtension>
          <ExtensionName>BGInfo</ExtensionName>
          <Publisher>Microsoft.Compute</Publisher>
          <Version>1.*</Version>
       </VMExtension>
    </VMExtensions>
  </ComputeNodes>
  <AutoGrowShrink>
    <CertificateId>1</CertificateId>
  </AutoGrowShrink>
</IaaSClusterConfig>

```

### <a name="example-3"></a>例 3
次の構成ファイルでは、既存のドメイン フォレストで HPC Pack クラスターが展開されます。 このクラスターには、ヘッド ノードが 1 つ、500 GB データ ディスクのデータベース サーバーが 1 つ、Windows Server 2012 R2 オペレーティング システムを実行するブローカー ノードが 2 つ、Windows Server 2012 R2 オペレーティング システムを実行する計算ノードが 5 つ含まれています。 クラウド サービス MyHPCCNService は、アフィニティ グループ *MyIBAffinityGroup* で作成されます。その他のクラウド サービスはすべて、アフィニティ グループ *MyAffinityGroup* で作成されます。 HPC ジョブ スケジューラ REST API と HPC Web ポータルはヘッド ノードで有効になっています。

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>    
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>NewRemoteDB</DBOption>
    <DBVersion>SQLServer2014_Enterprise</DBVersion>
    <DBServer>
      <VMName>MyDBServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>ExtraLarge</VMSize>
      <DataDiskSizeInGB>500</DataDiskSizeInGB>
    </DBServer>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>A8</VMSize>
<NodeCount>5</NodeCount>
<AffinityGroup>MyIBAffinityGroup</AffinityGroup>
  </ComputeNodes>
  <BrokerNodes>
    <VMNamePattern>MyHPCBN-%0000%</VMNamePattern>
    <ServiceName>MyHPCBNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>2</NodeCount>
  </BrokerNodes>
</IaaSClusterConfig>
```



### <a name="example-4"></a>例 4
次の構成ファイルでは、既存のドメイン フォレストで HPC Pack クラスターが展開されます。 このクラスターには、ローカル データベースを持つヘッド ノードが 1 つ含まれます。2 つの Azure ノード テンプレートが作成されます。Azure ノード テンプレート *AzureTemplate1* に対して、中サイズの Azure ノードが 3 つ作成されます。 ヘッド ノードが構成されると、スクリプト ファイルがヘッド ノードで実行されます。

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
<VMSize>ExtraLarge</VMSize>
    <PostConfigScript>c:\MyHNPostActions.ps1</PostConfigScript>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
    <Certificate>
      <Id>2</Id>
      <PfxFile>d:\mytestcert2.pfx</PfxFile>
    </Certificate>    
  </Certificates>
  <AzureBurst>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate1</TemplateName>
      <SubscriptionId>bb9252ba-831f-4c9d-ae14-9a38e6da8ee4</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc1</ServiceName>
      <StorageAccount>myteststorage1</StorageAccount>
      <NodeCount>3</NodeCount>
      <RoleSize>Medium</RoleSize>
    </AzureNodeTemplate>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate2</TemplateName>
      <SubscriptionId>ad4b9f9f-05f2-4c74-a83f-f2eb73000e0b</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc2</ServiceName>
      <StorageAccount>myteststorage2</StorageAccount>
      <Proxy>
        <UsesStaticProxyCount>false</UsesStaticProxyCount>     
        <ProxyRatio>100</ProxyRatio>
        <ProxyRatioBase>400</ProxyRatioBase>
      </Proxy>
      <OSVersion>WindowsServer2012</OSVersion>
    </AzureNodeTemplate>
  </AzureBurst>
</IaaSClusterConfig>
```

## <a name="troubleshooting"></a>トラブルシューティング
* **“VNet が存在しない” エラー** - 1 つサブスクリプションの下でスクリプトを実行して Azure で複数のクラスターを同時にデプロイするとき、“VNet *VNet\_Name* が存在しない” というエラーで 1 つまたは複数のデプロイが失敗することがあります。
  このエラーが発生した場合、失敗したデプロイに対してスクリプトを再び実行してください。
* **Azure Virtual Network からインターネットにアクセスできない** - デプロイ スクリプトを利用し、新しいドメイン コントローラーを含むクラスターを作成する場合、あるいは手動でヘッド ノード VM をドメイン コントローラーに昇格する場合、VM をインターネットに接続できないことがあります。 この問題は、フォワーダー DNS サーバーがドメイン コントローラーで自動的に構成されるとき、このフォワーダー DNS サーバーが適切に解決しない場合に発生することがあります。
  
    この問題を回避するには、ドメイン コントローラーにログオンし、フォワーダー構成設定を削除するか、有効なフォワーダー DNS サーバーを構成します。 この設定を構成するには、サーバー マネージャーで、**[ツール]**  >
     **[DNS]** をクリックして DNS マネージャーを開き、**[フォワーダー]** をダブルクリックします。
* **コンピューティング集中型 VM から RDMA ネットワークにアクセスできない** - A8 や A9 などの RDMA 対応サイズを使用し、Windows Server コンピューティング ノードまたはブローカー ノード VM を追加する場合、これらの VM を RDMA アプリケーション ネットワークに接続できないことがあります。 この問題が発生する原因の 1 つとして、VM がクラスターに追加されたとき、HpcVmDrivers 拡張機能が適切にインストールされていないことがあります。 たとえば、拡張機能のインストールが途中で止まります。
  
    この問題を回避するには、最初に VM の拡張機能の状態を確認します。 拡張機能が適切にインストールされていない場合、HPC クラスターからノードを削除し、ノードを再度追加してみます。 たとえば、ヘッド ノードで Add-HpcIaaSNode.ps1 スクリプトを実行し、コンピューティング ノード VM を追加できます。

## <a name="next-steps"></a>次のステップ
* クラスターでテスト ワークロードを実行してみます。 たとえば、HPC Pack [ファースト ステップ ガイド](https://technet.microsoft.com/library/jj884144)を参照してください。
* クラスターのデプロイ スクリプトを作成して HPC ワークロードを実行するチュートリアルについては、「 [Azure で HPC Pack クラスターを開始して Excel と SOA ワークロードを実行する](../../virtual-machines-windows-excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。
* HPC Pack のツールを試し、作成したクラスターからコンピューティング ノードを開始、停止、追加、削除してください。 「 [Manage compute nodes in an HPC Pack cluster in Azure (Azure の HPC Pack クラスターでコンピューティング ノードを管理する)](hpcpack-cluster-node-manage.md)」をご覧ください。
* ローカル コンピューターからクラスターにジョブを送信するための設定については、「 [オンプレミス コンピューターから Azure にデプロイされた HPC Pack クラスターに HPC ジョブを送信する](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。


