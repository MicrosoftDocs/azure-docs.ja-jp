---
title: "静的内部プライベート IP - Azure VM - クラシック"
description: "静的内部 IP  (DIP) とそれらを管理する方法を理解します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: 93444c6f-af1b-41f8-a035-77f5c0302bf0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: d2e2effa9c215107cf0893a74df0b909fbf5d4c0
ms.lasthandoff: 03/22/2017


---
# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>PowerShell を使用した静的内部プライベート IP アドレスの設定方法 (クラシック)
ほとんどの場合は、仮想マシンに対して静的内部 IP アドレスを指定する必要はありません。 仮想ネットワーク内の VM は、指定された範囲の内部 IP アドレスを自動的に受け取るからです。 ただし、場合によっては、特定の VM に対して静的 IP アドレスを指定したほうが効果的な場合があります。 たとえば、VM が、DNS を実行したり、ドメイン コントローラーとして機能する場合です。 静的内部 IP アドレスは、VM が停止またはプロビジョニング解除された状態になっても VM で保持されます。 

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、[Resource Manager とクラシックの](../azure-resource-manager/resource-manager-deployment-model.md) 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイメントでは、[Resource Manager デプロイメント モデル](virtual-networks-static-private-ip-arm-ps.md)を使用することをお勧めします。
> 
> 

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>特定の IP アドレスが使用可能であるかを確認する方法
IP アドレス *10.0.0.7* が *TestVnet* という VNet で使用可能かどうかを確認するには、次の PowerShell コマンドを実行して、*IsAvailable* の値を確認します。

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

> [!NOTE]
> 上記のコマンドを安全な環境でテストする場合は、[Virtual Network の作成](virtual-networks-create-vnet-classic-portal.md)のガイドラインに従い、*TestVnet* という VNet を作成して *10.0.0.0/8* アドレス空間が使用されるようにします。
> 
> 

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>VM 作成時に静的内部 IP アドレスを指定する方法
以下の PowerShell スクリプトによって *TestService* という新しいクラウド サービスが作成され、Azure からイメージが取得されます。次に、その取得されたイメージを使用して *TestVM* という VM が新しいクラウド サービス内に作成され、その VM はサブネット *Subnet-1*に含まれるように設定され、VM の静的内部 IP として *10.0.0.7* が指定されます。

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzureSubnet –SubnetNames Subnet-1 `
    | Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
    | New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>VM 用の静的内部 IP 情報を取得する方法
上記のスクリプトで作成した VM の静的内部 IP 情報を表示するには、次の PowerShell コマンドを実行し、 *IpAddress*の値を確認します。

    Get-AzureVM -Name TestVM -ServiceName TestService

    DeploymentName              : TestService
    Name                        : TestVM
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 10.0.0.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : TestVM
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : rsR2-797
    AvailabilitySetName         : 
    DNSName                     : http://testservice000.cloudapp.net/
    Status                      : Provisioning
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 
    PublicIPName                : 
    NetworkInterfaces           : {}
    ServiceName                 : TestService
    OperationDescription        : Get-AzureVM
    OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
    OperationStatus             : OK

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>VM から静的内部 IP を削除する方法
上記のスクリプトで VM に追加された静的内部 IP を削除するには、次の PowerShell コマンドを実行します。

    Get-AzureVM -ServiceName TestService -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>既存の VM に静的内部 IP を追加する方法
上記のスクリプトを使用して作成した VM に静的内部 IP アドレスを追加するには、次のコマンドを実行します。

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
    | Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
    | Update-AzureVM

## <a name="next-steps"></a>次のステップ
[予約済み IP](virtual-networks-reserved-public-ip.md)

[インスタンスレベル パブリック IP (ILPIP)](virtual-networks-instance-level-public-ip.md)

[予約済み IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)


