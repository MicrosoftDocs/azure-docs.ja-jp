---
title: Azure で Packer を使用して Windows VM のイメージを作成する方法 | Microsoft Docs
description: Packer を使用して Azure に Windows 仮想マシンのイメージを作成する方法について説明します。
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/22/2019
ms.author: cynthn
ms.openlocfilehash: f768582e8ef32bc654a2f797c5c7a481a26fb643
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734185"
---
# <a name="how-to-use-packer-to-create-windows-virtual-machine-images-in-azure"></a>Packer を使用して Azure に Windows 仮想マシンのイメージを作成する方法
Azure の各仮想マシン (VM) は、Windows ディストリビューションと OS のバージョンを定義するイメージから作成されます。 イメージには、プリインストールされているアプリケーションと構成を含めることができます。 Azure Marketplace には、ほとんどの OS およびアプリケーション環境用の自社製およびサード パーティ製のイメージが数多く用意されています。また、ニーズに合わせて独自のイメージを作成することもできます。 この記事では、オープン ソース ツール [Packer](https://www.packer.io/) を使用して Azure に独自のイメージを定義およびビルドする方法について、詳しく説明します。

この記事は、2019 年 2 月 21 日に [Az PowerShell モジュール](https://docs.microsoft.com/powershell/azure/install-az-ps) バージョン 1.3.0 と [Packer](https://www.packer.io/docs/install/index.html) バージョン 1.3.4 を使用して最後にテストされました。

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="create-azure-resource-group"></a>Azure リソース グループを作成する
ビルド プロセス中、Packer はソース VM をビルドする際に一時的な Azure リソースを作成します。 イメージとして使用するためにそのソース VM をキャプチャするには、リソース グループを定義する必要があります。 Packer のビルド プロセスからの出力は、このリソース グループに格納されます。

[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) を使用して Azure リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurepowershell
$rgName = "mypackerGroup"
$location = "East US"
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>Azure 資格情報の作成
Packer はサービス プリンシパルを使用して Azure で認証されます。 Azure のサービス プリンシパルは、アプリケーション、サービス、および Packer などのオートメーション ツールで使用できるセキュリティ ID です。 Azure でサービス プリンシパルが実行できる操作を設定するアクセス許可の制御と定義を行います。

[New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal) を使用してサービス プリンシパルを作成し、そのサービス プリンシパルにアクセス許可を割り当て、[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment) を使用してリソースを作成および管理します。 `-DisplayName` の値は一意である必要があります。必要に応じて独自の値に置き換えてください。  

```azurepowershell
$sp = New-AzADServicePrincipal -DisplayName "PackerServicePrincipal"
$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret)
$plainPassword = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)
New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

その後、パスワードとアプリケーション ID を出力します。

```powershell
$plainPassword
$sp.ApplicationId
```


Azure に対して認証するには、[Get-AzSubscription](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription) を使用して Azure のテナントとサブスクリプション ID も取得する必要があります。

```powershell
Get-AzSubscription
```


## <a name="define-packer-template"></a>Packer テンプレートを定義する
イメージをビルドするには、テンプレートを JSON ファイルとして作成します。 テンプレートでは、実際のビルド プロセスを実行するビルダーとプロビジョナーを定義します。 Packer には [Azure 用のビルダー](https://www.packer.io/docs/builders/azure.html)が用意されており、前の手順で作成したサービス プリンシパルの資格情報などの Azure リソースを定義できます。

*windows.json* というファイルを作成し、次のコンテンツを貼り付けます。 次に対応する実際の値を入力します。

| パラメーター                           | 入手場所 |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | `$sp.applicationId` でサービス プリンシパル ID を表示します |
| *client_secret*                     | `$plainPassword` を使用して自動生成されたパスワードを表示します |
| *tenant_id*                         | `$sub.TenantId` コマンドからの出力 |
| *subscription_id*                   | `$sub.SubscriptionId` コマンドからの出力 |
| *managed_image_resource_group_name* | 最初の手順で作成したリソース グループの名前 |
| *managed_image_name*                | 作成されるマネージド ディスク イメージの名前 |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "client_secret": "ppppppp-pppp-pppp-pppp-ppppppppppp",
    "tenant_id": "zzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz",
    "subscription_id": "yyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyy",

    "managed_image_resource_group_name": "myPackerGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Windows",
    "image_publisher": "MicrosoftWindowsServer",
    "image_offer": "WindowsServer",
    "image_sku": "2016-Datacenter",

    "communicator": "winrm",
    "winrm_use_ssl": true,
    "winrm_insecure": true,
    "winrm_timeout": "3m",
    "winrm_username": "packer",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "type": "powershell",
    "inline": [
      "Add-WindowsFeature Web-Server",
      "& $env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit",
      "while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 10  } else { break } }"
    ]
  }]
}
```

このテンプレートは、Windows Server 2016 VM を作成し、IIS をインストールした後、Sysprep で VM を一般化します。 IIS のインストールでは、PowerShell プロビジョナーを使用して追加のコマンドを実行する方法が示されます。 最終的な Packer イメージには、必要なソフトウェアのインストールと構成が含まれます。


## <a name="build-packer-image"></a>Packer イメージをビルドする
まだローカル コンピューターに Packer がインストールされていない場合は、[手順に従って Packer をインストールしてください](https://www.packer.io/docs/install/index.html)。

コマンド プロンプトを開き、次のように Packer テンプレート ファイルを指定して、イメージをビルドします。

```
./packer build windows.json
```

上記のコマンドの出力例は、次のとおりです。

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> task : Image deployment
==> azure-arm:  ->> dept : Engineering
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the certificate’s URL ...
==> azure-arm:  -> Key Vault Name        : ‘pkrkvpq0mthtbtt’
==> azure-arm:  -> Key Vault Secret Name : ‘packerKeyVaultSecret’
==> azure-arm:  -> Certificate URL       : ‘https://pkrkvpq0mthtbtt.vault.azure.net/secrets/packerKeyVaultSecret/8c7bd823e4fa44e1abb747636128adbb'
==> azure-arm: Setting the certificate’s URL ...
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.55.35’
==> azure-arm: Waiting for WinRM to become available...
==> azure-arm: Connected to WinRM!
==> azure-arm: Provisioning with Powershell...
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-powershell-provisioner902510110
    azure-arm: #< CLIXML
    azure-arm:
    azure-arm: Success Restart Needed Exit Code      Feature Result
    azure-arm: ------- -------------- ---------      --------------
    azure-arm: True    No             Success        {Common HTTP Features, Default Document, D...
    azure-arm: <Objs Version=“1.1.0.1” xmlns=“http://schemas.microsoft.com/powershell/2004/04"><Obj S=“progress” RefId=“0"><TN RefId=“0”><T>System.Management.Automation.PSCustomObject</T><T>System.Object</T></TN><MS><I64 N=“SourceId”>1</I64><PR N=“Record”><AV>Preparing modules for first use.</AV><AI>0</AI><Nil /><PI>-1</PI><PC>-1</PC><T>Completed</T><SR>-1</SR><SD> </SD></PR></MS></Obj></Objs>
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-pq0mthtbtt/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Compute Name              : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Packer が VM をビルド、プロビジョナーを実行、およびデプロイメントをクリーンアップするのに数分かかります。


## <a name="create-a-vm-from-the-packer-image"></a>Packer イメージから VM を作成する
[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) を使用して、イメージから VM を作成できるようになりました。 これらが存在しない場合は、サポート ネットワーク リソースが作成されます。 入力を求められたら、VM で作成された管理用のユーザー名とパスワードを入力します。 次の例では、*myPackerImage* から *myVM* という名前の VM を作成します。

```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Image "myPackerImage"
```

自分の Packer イメージとは異なるリソース グループまたはリージョンで VM を作成する場合は、イメージ名ではなく、イメージ ID を指定します。 [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/Get-AzImage) を使用してイメージ ID を取得できます。

Packer イメージから VM を作成するには数分かかります。


## <a name="test-vm-and-webserver"></a>VM と Web サーバーのテスト
VM のパブリック IP アドレスを取得するには、[Get-AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) を使用します。 次の例では、先ほど作成した *myPublicIP* の IP アドレスを取得しています。

```powershell
Get-AzPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

Packer プロビジョナーからの IIS インストールを含む VM が動いていることを確認するには、Web ブラウザーにでパブリック IP アドレスを入力します。

![IIS の既定のサイト](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>次の手順
この例では、Packer を使用して IIS が既にインストールされた VM イメージを作成しました。 この VM イメージは、アプリを、Azure DevOps Services、Ansible、Chef、Puppet でイメージから作成した VM にデプロイするなど、既存のデプロイ ワークフローとともに使用できます。

他の Windows ディストリビューション用の追加の Packer テンプレートの例については、[この GitHub リポジトリ](https://github.com/hashicorp/packer/tree/master/examples/azure)をご覧ください。
