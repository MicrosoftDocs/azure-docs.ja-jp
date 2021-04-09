---
title: Azure CLI を使用して DevTest Labs で仮想マシンを作成して管理する
description: Azure DevTest Labs を使って Azure CLI で仮想マシンを作成して管理する方法を説明します
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 22ee6bf607fe1b66cece0e7ddb25a2da2830258b
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201466"
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
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
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

[SSH](../virtual-machines/linux/mac-create-ssh-keys.md) または[リモート デスクトップ](../virtual-machines/windows/connect-logon.md)を使って VM に接続します。
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

### <a name="list-artifacts-available-in-the-lab"></a>ラボで使用できるアーティファクトの一覧を表示する

ラボ内の VM で使用可能な成果物の一覧を表示するには、次のコマンドを実行します。

**Cloud Shell - PowerShell**: $expand で $ の前にバッククォート (\`) が使用されていることに注意してください (つまり `$expand)。

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(`$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

**Cloud Shell - Bash**: コマンドで $ の前にスラッシュ (\\) 文字が使用されていることに注意してください。 

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(\$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

サンプル出力: 

```json
[
  {
    "artifactId": "/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DevTestLab/labs/<lab name>/artifactSources/public repo/artifacts/windows-7zip",
    "status": "Succeeded"
  }
]
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

## <a name="next-steps"></a>次のステップ
次のコンテンツを参照してください: [Azure DevTest Labs の Azure CLI ドキュメント](/cli/azure/lab)。 
