---
title: "カスタムのドメイン コント ローラーにクラウド サービスを接続する | Microsoft Docs"
description: "PowerShell と AD ドメイン拡張機能を使用してカスタム AD ドメインに Web ロールまたは worker ロールを接続する方法について説明します"
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 1e2d7c87-d254-4e7a-a832-67f84411ec95
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 0b404af5b638ec2d543ce98b562b7df538652f70
ms.openlocfilehash: 39ee9cc1027958d85b2af2781adab0fe06c9a433
ms.lasthandoff: 01/05/2017


---
# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Azure でホストされているカスタム AD ドメイン コントローラーへの Azure Cloud Services ロールの接続
最初に、Azure に Virtual Network (VNet) を設定します。 次に、(Azure 仮想マシンでホストされている) Active Directory ドメイン コントローラーを VNet に追加します。 次に、事前に作成した VNet に既存のクラウド サービス ロールを追加した後、それらをドメイン コントローラーに接続します。

作業を開始するにあたり、いくつか注意することがあります。

1. このチュートリアルでは PowerShell を使用します。Azure PowerShell がインストールされ、使用する準備が整っていることを確認してください。 Azure PowerShell の設定方法については、「[Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs)」を参照してください。
2. AD ドメイン コントローラーと Web ロールまたは worker ロールのインスタンスは VNet 内にある必要があります。

このステップ バイ ステップ ガイドに従って作業しているときに問題が発生した場合はコメントを残してください。 私たちはコメントを必ず読んでいるので、だれかが対応します。

クラウド サービスによって参照されるネットワークは、**従来の仮想ネットワーク**にする必要があります。

## <a name="create-a-virtual-network"></a>Virtual Network の作成
Azure に仮想ネットワークを作成するには、Azure クラシック ポータルか PowerShell を使います。 このチュートリアルでは、PowerShell を使用します。 Azure クラシック ポータルを使用して Virtual Network を作成するには、「 [仮想ネットワークの作成](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)」を参照してください。

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
    <VirtualNetworkConfiguration>
    <VirtualNetworkSites>
        <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
        <AddressSpace>
            <AddressPrefix>[your-address-prefix]</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="[your-subnet-name]">
            <AddressPrefix>[your-subnet-range]</AddressPrefix>
            </Subnet>
        </Subnets>
        </VirtualNetworkSite>
    </VirtualNetworkSites>
    </VirtualNetworkConfiguration>
</NetworkConfiguration>
"@;

$vnetConfigPath = "<path-to-vnet-config>"
Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath
```

## <a name="create-a-virtual-machine"></a>仮想マシンを作成する
Virtual Network の設定が完了したら、AD ドメイン コントローラーを作成する必要があります。 このチュートリアルでは、Azure 仮想マシンに AD ドメイン コントローラーを設定します。

そのためには、PowerShell で次のコマンドを使用して仮想マシンを作成します。

```powershell
# Initialize variables
# VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.

$vnetname = '<your-vnet-name>'
$subnetname = '<your-subnet-name>'
$vmsvc1 = '<your-hosted-service>'
$vm1 = '<your-vm-name>'
$username = '<your-username>'
$password = '<your-password>'
$affgrp = '<your- affgrp>'

# Create a VM and add it to the Virtual Network

New-AzureQuickVM -Windows -ServiceName $vmsvc1 -Name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname
```

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>仮想マシンをドメイン コントローラーに昇格させる
仮想マシンを AD ドメイン コントローラーとして構成するには、VM にログインして構成する必要があります。

VM にログインするには、PowerShell で次のコマンドを使用して RDP ファイルを取得します。

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

VM にログインした後、 [カスタム AD ドメイン コントローラーの設定方法](http://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx)に関するステップ バイ ステップ ガイドに従って仮想マシンを AD ドメイン コントローラーとして設定します。

## <a name="add-your-cloud-service-to-the-virtual-network"></a>クラウド サービスを Virtual Network に追加する
次に、先ほど作成した VNet にクラウド サービス デプロイメントを追加する必要があります。 そのためには、Visual Studio または任意のエディターを使用して cscfg に該当するセクションを追加して、クラウド サービスの cscfg を変更します。

```xml
<ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
    <Role name="[role-name]">
    <Instances count="[number-of-instances]" />
    </Role>
    <NetworkConfiguration>

    <!--optional-->
    <Dns>
        <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
    </Dns>
    <!--optional-->

    <!--VNet settings
        VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.-->
    <VirtualNetworkSite name="[virtual-network-name]" />
    <AddressAssignments>
        <InstanceAddress roleName="[role-name]">
        <Subnets>
            <Subnet name="[subnet-name]" />
        </Subnets>
        </InstanceAddress>
    </AddressAssignments>
    <!--VNet settings-->

    </NetworkConfiguration>
</ServiceConfiguration>
```

次に、クラウド サービス プロジェクトをビルドして Azure にデプロイします。 クラウド サービス パッケージを Azure にデプロイする方法については、「 [クラウド サービスを作成して展開する方法](cloud-services-how-to-create-deploy.md#how-to-deploy-a-cloud-service)

## <a name="connect-your-webworker-roles-to-the-domain"></a>ドメインに Web/worker ロールを接続する
Azure にクラウド サービス プロジェクトをデプロイした後は、AD ドメイン拡張機能を使用して、ロール インスタンスをカスタム AD ドメインに接続します。 AD ドメイン拡張機能を既存のクラウド サービス デプロイに追加し、カスタム ドメインに参加させるには、PowerShell で次のコマンドを実行します。

```powershell
# Initialize domain variables

$domain = '<your-domain-name>'
$dmuser = '$domain\<your-username>'
$dmpswd = '<your-domain-password>'
$dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force
$dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd)

# Add AD Domain Extension to the cloud service roles

Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35
```

以上です。

クラウド サービスがカスタム ドメイン コントローラーに参加します。 AD ドメイン拡張機能を構成するための他のオプションを調べるには、次のように PowerShell のヘルプを使用します。

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```

