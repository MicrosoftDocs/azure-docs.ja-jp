---
title: "Azure CLI を使用して DevTest Labs で仮想マシンを作成して管理する | Microsoft Docs"
description: "Azure DevTest Labs を使って Azure CLI 2.0 で仮想マシンを作成して管理する方法を説明します"
services: devtest-lab,virtual-machines
documentationcenter: na
author: lisawong19
manager: douge
editor: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: liwong
ms.openlocfilehash: a3af12ba0598d60b55b3714ae1690fe3e5b54a42
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Azure CLI を使用して DevTest Labs で仮想マシンを作成して管理する
このクイック スタートでは、ラボで開発用コンピューターを作成、起動、接続、更新、およびクリーンアップする方法について説明します。 

作業を開始する前に、次のことを行います。

* ラボが作成されていない場合は、[こちら](devtest-lab-create-lab.md)で方法をご覧ください。

* [CLI 2.0 をインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)します。 開始するには、az login を実行して、Azure との接続を作成します。 

## <a name="create-and-verify-the-virtual-machine"></a>仮想マシンを作成して確認する 
SSH 認証を使って Marketplace イメージから VM を作成します。
```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```
> [!NOTE]
> **ラボのリソース グループ**名を --resource-group パラメーターに設定します。
>

数式を使って VM を作成する場合は、[az lab vm create](https://docs.microsoft.com/cli/azure/lab/vm#az_lab_vm_create) で --formula パラメーターを使います。


VM が使用可能であることを確認します。
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
VM を起動します。
```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```
> [!NOTE]
> **ラボのリソース グループ**名を --resource-group パラメーターに設定します。
>

[SSH](../virtual-machines/linux/mac-create-ssh-keys.md) または[リモート デスクトップ](../virtual-machines/windows/connect-logon.md)を使って VM に接続します。
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>仮想マシンを更新する
アーティファクトを VM に適用します。
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
VM を停止します。
```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

VM を削除します。
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]