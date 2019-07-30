---
title: クイック スタート - Azure Container Instances への Docker コンテナーのデプロイ - PowerShell
description: このクイック スタートでは、Azure PowerShell を使用して、分離された Azure コンテナー インスタンスで実行されているコンテナー化された Web アプリをすばやくデプロイします
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 7fe199d2ac228ddb0ccfd1e5bc980e680e160acf
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325838"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-azure-powershell"></a>クイック スタート:Azure PowerShell を使用してコンテナー インスタンスを Azure にデプロイする

サーバーレスの Docker コンテナーを Azure で簡単にすばやく実行するには、Azure Container Instances を使用します。 Azure Kubernetes Service のように完全なコンテナー オーケストレーション プラットフォームが不要な場合は、コンテナー インスタンス オンデマンドにアプリケーションをデプロイします。

このクイック スタートでは、Azure PowerShell を使用して、分離された Windows コンテナーをデプロイし、そのアプリケーションを完全修飾ドメイン名 (FQDN) を介して使用できるようにします。 1 つのデプロイ コマンドを実行して数秒後には、コンテナーで実行中のアプリケーションを参照できます。

![Azure Container Instances にデプロイされたアプリのブラウザーでの表示][qs-powershell-01]

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell をローカル環境にインストールして使用する場合、このチュートリアルでは Azure PowerShell モジュールが必要です。 バージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-a-resource-group"></a>リソース グループの作成

Azure のコンテナー インスタンスは、すべての Azure リソースと同様に、リソース グループにデプロイする必要があります。 リソース グループを使用すると、関連する Azure リソースを整理して管理できます。

まず、次の [New-AzResourceGroup][New-AzResourceGroup] コマンドを使用して、*myResourceGroup* という名前のリソース グループを *eastus* の場所に作成します。

 ```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>コンテナーを作成する

リソース グループを作成すると、Azure でコンテナーを実行できます。 Azure PowerShell を使用してコンテナー インスタンスを作成するには、リソース グループ名、コンテナー インスタンス名、Docker コンテナー イメージを [New-AzContainerGroup][New-AzContainerGroup] コマンドレットに指定します。 このクイック スタートでは、パブリックの `mcr.microsoft.com/windows/servercore/iis:nanoserver` イメージを使用します。 このイメージには、Nano Server で動作する Microsoft インターネット インフォメーション サービス (IIS) がパッケージされています。

1 つまたは複数の開くポート、DNS 名ラベル、またはその両方を指定することで、コンテナーをインターネットに公開することができます。 このクイック スタートでは、IIS にパブリックに到達できるよう、DNS 名ラベルを指定してコンテナーをデプロイします。

次のようなコマンドを実行して、コンテナー インスタンスを開始します。 `-DnsNameLabel` の値は、インスタンスを作成する Azure リージョン内で一意の値を設定してください。 エラー メッセージ "DNS 名ラベルは利用できません" が表示された場合は、別の DNS 名ラベルを試してください。

 ```azurepowershell-interactive
New-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image mcr.microsoft.com/windows/servercore/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

数秒以内に、Azure から応答を受信します。 コンテナーの `ProvisioningState` は、最初は **Creating** と表示されますが、1、2 分で **Succeeded** に変わります。 [Get-AzContainerGroup][Get-AzContainerGroup] コマンドレットを使用して、デプロイの状態を確認します。

 ```azurepowershell-interactive
Get-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

コンテナーのプロビジョニング状態、完全修飾ドメイン名 (FQDN)、IP アドレスがコマンドレットの出力に表示されます。

```console
PS Azure:\> Get-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer


ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            : Always
IpAddress                : 52.226.19.87
DnsNameLabel             : aci-demo-win
Fqdn                     : aci-demo-win.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Windows
Volumes                  :
State                    : Pending
Events                   : {}
```

コンテナーの `ProvisioningState` が **Succeeded** になったら、ブラウザーでその `Fqdn` に移動します。 次のような Web ページが表示されたら成功です。 Docker コンテナーで実行されているアプリケーションが Azure に正常にデプロイされました。

![ブラウザーに表示された、Azure Container Instances を使用してデプロイされた IIS][qs-powershell-01]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

コンテナーでの処理が完了したら、[Remove-AzContainerGroup][Remove-AzContainerGroup] コマンドレットを使用してそのコンテナーを削除します。

 ```azurepowershell-interactive
Remove-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、パブリック Docker Hub レジストリ内のイメージから Azure コンテナー インスタンスを作成しました。 コンテナー イメージをビルドし、プライベート Azure コンテナー レジストリからデプロイする場合は、Azure Container Instances のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure Container Instances のチュートリアル](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzResourceGroup]: /powershell/module/az.resources/new-Azresourcegroup
[New-AzContainerGroup]: /powershell/module/az.containerinstance/new-Azcontainergroup
[Get-AzContainerGroup]: /powershell/module/az.containerinstance/get-Azcontainergroup
[Remove-AzContainerGroup]: /powershell/module/az.containerinstance/remove-Azcontainergroup
