---
title: "クイック スタート - PowerShell を使用した最初の Azure Container Instances コンテナーの作成"
description: "PowerShell を使用して Windows コンテナー インスタンスを作成することによって、Azure Container Instances の使用を開始します。"
services: container-instances
documentationcenter: 
author: mmacy
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: marsma
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 0cc6612a91532774a2645676e36f617ddc5de12c
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="create-your-first-container-in-azure-container-instances"></a>Azure Container Instances での最初のコンテナーの作成

Azure Container Instances を使用すると、仮想マシンをプロビジョニングしたり、より高度なレベルのサービスを採用したりしなくても、Azure の Docker コンテナーを簡単に作成、管理できます。

このクイック スタートでは、Azure で Windows コンテナーを作成し、パブリック IP アドレスを使用してインターネットに公開します。 この操作は、1 つのコマンドで完結します。 すぐに、お使いのブラウザーに次のように表示されます。

![Azure Container Instances を使用してデプロイされたアプリのブラウザーでの表示][qs-powershell-01]

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

このクイック スタートには、Azure PowerShell モジュール バージョン 4.4 以降が必要です。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。

## <a name="log-in-to-azure"></a>Azure へのログイン

`Login-AzureRmAccount` コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Create resource group

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用して Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>コンテナーを作成する

コンテナーを作成するには、名前、Docker イメージ、および Azure リソース グループを指定します。 コンテナーは、必要に応じて、パブリック IP アドレスを使用してインターネットに公開できます。 この場合は、インターネット インフォメーション サービス (IIS) を実行している Windows Nano サーバー コンテナーを使用します。

```powershell
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -IpAddressType Public
```

数秒以内に、あなたのリクエストに対する応答を得るでしょう。 最初に、コンテナーは**作成中**の状態になりますが、1 ～ 2 分のうちに起動されます。 `Get-AzureRmContainerGroup` コマンドレットを使用して状態を確認できます。

```powershell
Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

コンテナーのプロビジョニング状態と IP アドレスがコマンドレットの出力に表示されます。

```
ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/12345678-1234-1234-1234-12345678abcd/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            :
IpAddress                : 40.71.248.73
Ports                    : {80}
OsType                   : Windows
Volumes                  :
```

コンテナーの **ProvisioningState** が `Succeeded` に移行すると、指定した IP アドレスを使用して、お使いのブラウザーでこのコンテナーにアクセスできます。

![ブラウザーに表示された、Azure Container Instances を使用してデプロイされた IIS][qs-powershell-01]

## <a name="delete-the-container"></a>コンテナーを削除する

コンテナーを完了したら、`Remove-AzureRmContainerGroup` コマンドレットを使用してそのコンテナーを削除できます。

```powershell
Remove-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、事前に構築された Windows コンテナーを Azure Container Instances で起動しました。 Azure Container Registry を使用してコンテナーのビルドと Azure Container Instances へのデプロイを自分で試す場合は、Azure Container Instances のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure Container Instances のチュートリアル](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png
