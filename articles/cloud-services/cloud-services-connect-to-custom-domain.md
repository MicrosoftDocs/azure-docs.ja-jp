---
title: カスタムのドメイン コント ローラーにクラウド サービスを接続する | Microsoft Docs
description: PowerShell と AD ドメイン拡張機能を使ってカスタム AD ドメインに Web ロールまたは worker ロールを接続する方法について説明します
services: cloud-services
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: gwallace
ms.openlocfilehash: 97a24720e65539a68745a5a1bb3f13ce1cafb9be
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359170"
---
# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Azure でホストされているカスタム AD ドメイン コントローラーへの Azure Cloud Services ロールの接続
最初に、Azure に Virtual Network (VNet) を設定します。 次に、(Azure 仮想マシンでホストされている) Active Directory ドメイン コントローラーを VNet に追加します。 次に、事前に作成した VNet に既存のクラウド サービス ロールを追加した後、それらをドメイン コントローラーに接続します。

作業を開始するにあたり、いくつか注意することがあります。

1. このチュートリアルでは PowerShell を使用します。Azure PowerShell がインストールされ、使用する準備が整っていることを確認してください。 Azure PowerShell の設定方法については、「[Azure PowerShell のインストールと構成の方法](/powershell/azure/overview)」を参照してください。
2. AD ドメイン コントローラーと Web ロールまたは worker ロールのインスタンスは VNet 内にある必要があります。

このステップ バイ ステップ ガイドに従って作業しているときに問題が発生した場合は、この記事の最後にコメントを残してください。 私たちはコメントを必ず読んでいるので、だれかが対応します。

クラウド サービスによって参照されるネットワークは、**従来の仮想ネットワーク**にする必要があります。

## <a name="create-a-virtual-network"></a>仮想ネットワークを作成します
Azure に Virtual Network を作成するには、Azure Portal か PowerShell を使います。 このチュートリアルでは、PowerShell を使用します。 Azure Portal を使用して仮想ネットワークを作成するには、[仮想ネットワークの作成](../virtual-network/quick-create-portal.md)に関する記事をご覧ください。 この記事では、仮想ネットワークの作成 (Resource Manager) について説明しますが、クラウド サービス用の仮想ネットワーク (クラシック) はお客様が作成する必要があります。 これには、ポータルで **[リソースの作成]** を選択し、 **[検索]** ボックスに「*仮想ネットワーク*」と入力して、**Enter** キーを押します。 検索結果の **[すべて]** の下で **[仮想ネットワーク]** を選択します。 **[デプロイ モデルの選択]** で **[クラシック]** を選択し、 **[作成]** をクリックします。 その後は、この記事の手順を実行できます。

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
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

VM にサインインした後、[カスタム AD ドメイン コントローラーの設定方法](https://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx)に関するステップ バイ ステップ ガイドに従って仮想マシンを AD ドメイン コントローラーとして設定します。

## <a name="add-your-cloud-service-to-the-virtual-network"></a>クラウド サービスを Virtual Network に追加する
次に、新しい VNet にクラウド サービス デプロイメントを追加する必要があります。 そのためには、Visual Studio または任意のエディターを使用して cscfg に該当するセクションを追加して、クラウド サービスの cscfg を変更します。

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

次に、クラウド サービス プロジェクトをビルドして Azure にデプロイします。 クラウド サービス パッケージを Azure にデプロイする方法については、「 [クラウド サービスを作成して展開する方法](cloud-services-how-to-create-deploy-portal.md)

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

クラウド サービスがカスタム ドメイン コントローラーに結合されます。 AD ドメイン拡張機能を構成するための他のオプションを調べるには、PowerShell のヘルプを使用します。 いくつかの例を次に示します。

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```
