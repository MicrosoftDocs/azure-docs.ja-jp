---
title: Azure のインスタンス レベル パブリック IP (クラシック) アドレス | Microsoft Docs
description: インスタンス レベル パブリック IP (ILPIP) アドレスの概要と、PowerShell を使用してこれらを管理する方法について説明します。
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 07eef6ec-7dfe-4c4d-a2c2-be0abfb48ec5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2018
ms.author: genli
ms.openlocfilehash: d92832d1eee995e8883dc6c8ed0f58c9755e40f8
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058418"
---
# <a name="instance-level-public-ip-classic-overview"></a>インスタンス レベル パブリック IP (クラシック) の概要
インスタンス レベル パブリック IP (ILPIP) は、VM または Cloud Services ロール インスタンスが存在するクラウド サービスではなく、VM またはロール インスタンスに直接割り当てることができるパブリック IP アドレスです。 ILPIP は、クラウド サービスに割り当てられる仮想 IP (VIP) に代わるものではありません。 むしろ、VM またはロール インスタンスに直接接続するときに使用できる追加の IP アドレスです。

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、2 種類のデプロイ モデルがあります。[Resource Manager とクラシック](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)です。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 Resource Manager を使用して VM を作成することをお勧めします。 Azure における [IP アドレス](virtual-network-ip-addresses-overview-classic.md) の動作を理解しておく必要があります。

![Difference between ILPIP and VIP](./media/virtual-networks-instance-level-public-ip/Figure1.png)

図 1 に示すように、クラウド サービスには VIP を使用してアクセスするのに対して、個々 の VM には通常 "VIP:&lt;ポート番号&gt;" を使用してアクセスします。 ILPIP を特定の VM に割り当てることで、その IP アドレスを使用して VM に直接アクセスできます。

Azure でクラウド サービスを作成すると、対応する DNS A レコードが自動的に作成され、実際の VIP を使用するのではなく、完全修飾ドメイン名 (FQDN) を使用してサービスにアクセスできるようになります。 同じ処理が ILPIP に対しても行われ、ILPIP ではなく FQDN による VM またはロール インスタンスへのアクセスが可能になります。 たとえば、*contosoadservice* という名前のクラウド サービスを作成し、2 つのインスタンスで *contosoweb* という名前の Web ロールを構成し、.cscfg で `domainNameLabel` を *WebPublicIP* に設定した場合、Azure によってこれらのインスタンスに対して次の A レコードが登録されます。


* WebPublicIP.0.contosoadservice.cloudapp.net
* WebPublicIP.1.contosoadservice.cloudapp.net
* ...


> [!NOTE]
> 各 VM またはロール インスタンスに割り当てることができる ILPIP は 1 つだけです。 サブスクリプションにつき最大 5 つの ILPIP を使用できます。 マルチ NIC VM では ILPIP はサポートされていません。
> 
> 

## <a name="why-would-i-request-an-ilpip"></a>ILPIP を要求する理由
VM またはロール インスタンスに直接割り当てた IP アドレスで接続できるようにする場合は、クラウド サービスの VIP:&lt;ポート番号&gt; を使用する代わりに、VM またはロール インスタンスの ILPIP を要求します。

* **アクティブ FTP** - VM に ILPIP を割り当てることで、すべてのポートでトラフィックを受信できます。 VM でトラフィックを受信するために、エンドポイントは不要になります。  FTP プロトコルの詳細については、[FTP プロトコルの概要](https://en.wikipedia.org/wiki/File_Transfer_Protocol#Protocol_overview)に関するページを参照してください。
* **送信 IP** - VM からの送信トラフィックは、送信元である ILPIP にマップされ、ILPIP は外部エンティティに対して VM を一意に識別します。

> [!NOTE]
> これまで、ILPIP アドレスはパブリック IP (PIP) アドレスと呼ばれていました。
> 

## <a name="manage-an-ilpip-for-a-vm"></a>VM の ILPIP の管理
以下のタスクにより、ILPIP の作成、割り当て、VM からの削除を行うことができます。

### <a name="how-to-request-an-ilpip-during-vm-creation-using-powershell"></a>PowerShell を使用して VM の作成中に ILPIP を要求する方法
次の PowerShell スクリプトでは、*FTPService* という名前のクラウド サービスを作成し、Azure からイメージを取得します。取得したイメージを使用して *FTPInstance* という名前の VM を作成し、ILPIP を使用するように VM を設定して、新しいサービスにその VM を追加します。

```powershell
New-AzureService -ServiceName FTPService -Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

#Set "current" storage account for the subscription. It will be used as the location of new VM disk

Set-AzureSubscription -SubscriptionName <SubName> -CurrentStorageAccountName <StorageAccountName>

#Create a new VM configuration object

New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"

```
新しい VM ディスクの場所として別のストレージ アカウントを指定する場合は、**MediaLocation** パラメーターを使用できます。

```powershell
    New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
     -MediaLocation https://management.core.windows.net/<SubscriptionID>/services/storageservices/<StorageAccountName> `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"
```

### <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>VM の ILPIP 情報を取得する方法
前のスクリプトで作成した VM の ILPIP 情報を表示するには、次の PowerShell コマンドを実行し、*PublicIPAddress* と *PublicIPName* の値を確認します。

```powershell
Get-AzureVM -Name FTPInstance -ServiceName FTPService
```

予想される出力:
 
    DeploymentName              : FTPService
    Name                        : FTPInstance
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : ReadyRole
    IpAddress                   : 100.74.118.91
    InstanceStateDetails        : 
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : FTPInstance
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : FTPInstance
    AvailabilitySetName         : 
    DNSName                     : http://ftpservice888.cloudapp.net/
    Status                      : ReadyRole
    GuestAgentStatus            :   Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 104.43.142.188
    PublicIPName                : ftpip
    NetworkInterfaces           : {}
    ServiceName                 : FTPService
    OperationDescription        : Get-AzureVM
    OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
    OperationStatus             : OK

### <a name="how-to-remove-an-ilpip-from-a-vm"></a>VM から ILPIP を削除する方法
前のスクリプトで VM に追加された ILPIP を削除するには、次の PowerShell コマンドを実行します。

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Remove-AzurePublicIP | Update-AzureVM
```

### <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>既存の VM に ILPIP を追加する方法
前のスクリプトを使用して作成した VM に ILPIP を追加するには、次のコマンドを実行します。

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Set-AzurePublicIP -PublicIPName ftpip2 | Update-AzureVM
```

## <a name="manage-an-ilpip-for-a-cloud-services-role-instance"></a>Cloud Services ロール インスタンスの ILPIP の管理

Cloud Services ロール インスタンスに ILPIP を追加するには、次の手順を実行します。

1. 「[Cloud Services の構成方法](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg)」に記載された手順を実行して、クラウド サービスの.cscfg ファイルをダウンロードします。
2. `InstanceAddress` 要素を追加して .cscfg ファイルを更新します。 次のサンプルでは、*MyPublicIP* という名前の ILPIP を、*WebRole1* という名前のロール インスタンスに追加します。 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ILPIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
          <ConfigurationSettings>
        <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
          </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <InstanceAddress roleName="WebRole1">
        <PublicIPs>
          <PublicIP name="MyPublicIP" domainNameLabel="WebPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>
    ```
3. 「[Cloud Services の構成方法](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg)」に記載された手順を実行して、クラウド サービスの.cscfg ファイルをアップロードします。

### <a name="how-to-retrieve-ilpip-information-for-a-cloud-service"></a>クラウド サービスの ILPIP 情報を取得する方法
ロール インスタンスごとに ILPIP 情報を表示するには、次の PowerShell コマンドを実行し、*PublicIPAddress*、*PublicIPName*、*PublicIPDomainNameLabel*、および *PublicIPFqdns* の値を確認します。

```powershell
Add-AzureAccount

$roles = Get-AzureRole -ServiceName <Cloud Service Name> -Slot Production -RoleName WebRole1 -InstanceDetails

$roles[0].PublicIPAddress
$roles[1].PublicIPAddress
```

`nslookup` を使用して、サブドメインの A レコードのクエリを実行することもできます。

```batch
nslookup WebPublicIP.0.<Cloud Service Name>.cloudapp.net
``` 

## <a name="next-steps"></a>次の手順
* クラシック デプロイ モデルの [IP アドレス指定](virtual-network-ip-addresses-overview-classic.md) の仕組みを理解します。
* [予約済み IP](virtual-networks-reserved-public-ip.md)について学習します。
