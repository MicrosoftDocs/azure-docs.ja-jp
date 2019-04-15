---
title: Azure CLI を使用して DevTest Labs で仮想マシンを作成して管理する | Microsoft Docs
description: Azure DevTest Labs を使って Azure CLI で仮想マシンを作成して管理する方法を説明します
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: 48a30ef86cdb10b540ffe1231294542ccff87255
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58895632"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Azure CLI を使用して DevTest Labs で仮想マシンを作成して管理する
このクイック スタートでは、ラボで開発用コンピューターを作成、起動、接続、更新、およびクリーンアップする方法について説明します。 

作業を開始する前に、次のことを行います。

* ラボが作成されていない場合は、[こちら](devtest-lab-create-lab.md)で方法をご覧ください。

* [Azure CLI のインストール](/cli/azure/install-azure-cli)を実行します。 開始するには、az login を実行して、Azure との接続を作成します。 

## <a name="create-and-verify-the-virtual-machine"></a>仮想マシンを作成して確認する 
DevTest Labs 関連のコマンドを実行する前に、`az account set` コマンドを使用して適切な Azure コンテキストを設定します。

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

仮想マシンを作成するコマンドは `az lab vm create` です。 ラボのリソース グループ、ラボの名前、および仮想マシンの名前はすべて必須です。 残りの引数は、仮想マシンの種類によって変化します。

次のコマンドでは、Azure Marketplace から Windows ベースのイメージが作成されます。 イメージの名前は、Azure portal を使って仮想マシンを作成するときに表示されるものと同じです。 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3’ --admin-username 'AdminUser' --admin-password 'Password1!'
```

次のコマンドでは、ラボで使用可能なカスタム イメージに基づいて仮想マシンが作成されます。

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

**image-type** 引数が、**gallery** から **custom** に変更されています。 イメージの名前は、Azure portal で仮想マシンを作成する場合に表示されるものと一致します。

次のコマンドでは、SSH 認証を使って Marketplace イメージから VM が作成されます。

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

**image-type** パラメーターを **formula** に設定することで、数式に基づいて仮想マシンを作成することもできます。 仮想マシンに特定の仮想ネットワークを選択する必要がある場合は、**vnet-name** パラメーターと **subnet** パラメーターを使用します。 詳しくは、「[az lab vm create](/cli/azure/lab/vm#az-lab-vm-create)」をご覧ください。

## <a name="verify-that-the-vm-is-available"></a>VM が使用可能であることを確認します。
VM を起動して接続する前に、VM が利用可能であることを確認するには、`az lab vm show` コマンドを使用します。 

```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>仮想マシンを起動して接続する
次のコマンドの例では、VM が起動されます。

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

VM に接続します: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) または [Remote Desktop](../virtual-machines/windows/connect-logon.md)。
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>仮想マシンを更新する
次のサンプル コマンドでは、VM に成果物が適用されます。

```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

ラボで使用できるアーティファクトを一覧表示します。
```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand "properties(\$expand=artifacts)" --query 'artifacts[].{artifactId: artifactId, status: status}'
```
```json
{
  "artifactId": "/subscriptions/abcdeftgh1213123/resourceGroups/lisalab123RG822645/providers/Microsoft.DevTestLab/labs/lisalab123/artifactSources/public repo/artifacts/linux-install-nodejs",
  "status": "Succeeded"
}
```

## <a name="stop-and-delete-the-virtual-machine"></a>仮想マシンを停止して削除する    
次のサンプル コマンドでは、VM が停止されます。

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

VM を削除します。
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>次の手順
次のコンテンツをご覧ください: [Azure DevTest Labs に関する Azure CLI のドキュメント](/cli/azure/lab?view=azure-cli-latest)。 