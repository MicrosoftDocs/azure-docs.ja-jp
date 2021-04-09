---
title: Azure 上でスケーラブルなアプリケーション用の VM とストレージ アカウントを作成する
description: Azure Blob Storage を使用してスケーラブルなアプリケーションの実行に使用される VM をデプロイする方法について説明します。
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4b8c52b03cb6dec6096565e9eac26b7b2c4a30e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89073252"
---
# <a name="create-a-virtual-machine-and-storage-account-for-a-scalable-application"></a>スケーラブルなアプリケーションの仮想マシンおよびストレージ アカウントの作成

このチュートリアルは、シリーズの第 1 部です。 このチュートリアルでは、大量のランダム データを Azure ストレージ アカウントにアップロードおよびダウンロードするアプリケーションのデプロイ方法を示します。 完了すると、ストレージ アカウントへの大量データのアップロードおよびダウンロードを行った仮想マシン上で実行される、コンソール アプリケーションが提供されます。

シリーズの第 1 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * ストレージ アカウントの作成
> * 仮想マシンの作成
> * カスタム スクリプト拡張機能の構成

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Azure PowerShell モジュール Az バージョン 0.7 以降が必要になります。 バージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-a-resource-group"></a>リソース グループを作成する

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成
 
サンプルでは、Azure Storage アカウント内の BLOB コンテナーに 50 個の大規模ファイルをアップロードします。 ストレージ アカウントは、Azure Storage データ オブジェクトを格納してアクセスするための一意の名前空間を用意します。 [New-AzStorageAccount](/powershell/module/az.Storage/New-azStorageAccount) コマンドを使用して作成されたリソース グループ内に、ストレージ アカウントを作成します。

次のコマンドの `<blob_storage_account>` プレースホルダーを、BLOB ストレージ アカウントのグローバルな一意の名前に置き換えます。

```powershell-interactive
$storageAccount = New-AzStorageAccount -ResourceGroupName myResourceGroup `
  -Name "<blob_storage_account>" `
  -Location EastUS `
  -SkuName Standard_LRS `
  -Kind Storage `
```

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

仮想マシンの構成を作成します。 この構成には、仮想マシンのデプロイ時に使用される設定 (仮想マシンのイメージ、サイズ、認証構成など) が含まれています。 この手順の実行時に、資格情報の入力を求められます。 入力した値は、仮想マシンのユーザー名とパスワードとして構成されます。

[New-AzVM](/powershell/module/az.compute/new-azvm) を使用して、仮想マシンを作成します。

```azurepowershell-interactive
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "eastus"
$vmName = "myVM"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create a virtual network card and associate with public IP address
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName myVM -VMSize Standard_DS14_v2 | `
    Set-AzVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig

Write-host "Your public IP address is $($pip.IpAddress)"
```

## <a name="deploy-configuration"></a>構成のデプロイ

このチュートリアルでは、必ず仮想マシンにインストールされていなければならない前提条件があります。 カスタム スクリプト拡張機能は、次のタスクを完了する PowerShell スクリプトの実行に使用されます。

> [!div class="checklist"]
> * .NET core 2.0 のインストール
> * chocolatey のインストール
> * GIT のインストール
> * サンプル リポジトリの複製
> * NuGet パッケージの復元
> * ランダム データでの 50 1-GB ファイルの作成

次のコマンドレットを実行して、仮想マシンの構成の最終処理を行います。 この手順を完了するには、5 ～ 15 分かかります。

```azurepowershell-interactive
# Start a CustomScript extension to use a simple PowerShell script to install .NET core, dependencies, and pre-create the files to upload.
Set-AzVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location EastUS `
    -FileUri https://raw.githubusercontent.com/azure-samples/storage-dotnet-perf-scale-app/master/setup_env.ps1 `
    -Run 'setup_env.ps1' `
    -Name DemoScriptExtension
```

## <a name="next-steps"></a>次のステップ

シリーズの第 1 部では、ストレージ アカウントの作成、仮想マシンのデプロイ、そして、以下を行う方法など必要な前提条件での仮想マシンの構成について説明しました。

> [!div class="checklist"]
> * ストレージ アカウントの作成
> * 仮想マシンの作成
> * カスタム スクリプト拡張機能の構成

シリーズの第 2 部では、指数による再試行と並行処理を使用して大量データをストレージ アカウントにアップロードします。

> [!div class="nextstepaction"]
> [ストレージ アカウントに大量の大きなファイルを並列でアップロードする](storage-blob-scalable-app-upload-files.md)
