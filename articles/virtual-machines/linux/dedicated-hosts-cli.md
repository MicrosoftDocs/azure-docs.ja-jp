---
title: CLI を使用して VM とスケール セット インスタンスを専用ホストにデプロイする
description: Azure CLI を使用して VM とスケール セット インスタンスを専用ホストにデプロイします。
author: cynthn
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: how-to
ms.date: 11/12/2020
ms.author: cynthn
ms.openlocfilehash: 9d4117cafd665556fb60278aa4dc60dc14a27ada
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670521"
---
# <a name="deploy-to-dedicated-hosts-using-the-azure-cli"></a>Azure CLI を使用して専用ホストにデプロイする
 

この記事では、仮想マシン (VM) をホストするための Azure [専用ホスト](../dedicated-hosts.md)を作成する方法について説明します。 

Azure CLI バージョン 2.16.0 以降がインストールされていること、および `az login` を使用して Azure アカウントにサインインしていることを確認します。 


## <a name="limitations"></a>制限事項

- 専用ホストで使用できるサイズとハードウェアの種類は、リージョンによって異なります。 詳しくは、ホストの[価格のページ](https://aka.ms/ADHPricing) を参照してください。

## <a name="create-resource-group"></a>リソース グループの作成 
Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 az group create で、リソース グループを作成します。 次の例では、*myDHResourceGroup* という名前のリソース グループを "*米国東部*" の場所に作成します。

```azurecli-interactive
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="list-available-host-skus-in-a-region"></a>リージョンで利用可能なホスト SKU を一覧表示する

すべてのホスト SKU がすべてのリージョンおよび可用性ゾーンで使用できるわけではありません。 

専用ホストのプロビジョニングを開始する前に、ホストの可用性とオファーの制限事項を一覧表示します。 

```azurecli-interactive
az vm list-skus -l eastus2  -r hostGroups/hosts  -o table  
```
 
## <a name="create-a-host-group"></a>ホスト グループを作成する 

**ホスト グループ** は、専用ホストのコレクションを表すリソースです。 リージョンと可用性ゾーンにホスト グループを作成し、それにホストを追加します。 高可用性を計画する場合は、追加のオプションがあります。 専用ホストでは、次のいずれかまたは両方のオプションを使用できます。 
- 複数の可用性ゾーンにまたがります。 この場合は、使用する各ゾーンにホスト グループを用意する必要があります。
- 物理ラックにマップされる複数の障害ドメインにまたがります。 
 
どちらの場合も、ホスト グループに対して障害ドメイン数を指定する必要があります。 グループ内で障害ドメインをまたがりたくない場合は、障害ドメインの数を 1 にします。 

可用性ゾーンと障害ドメインの両方を使用することもできます。 


この例では、[az vm host group create](/cli/azure/vm/host/group#az-vm-host-group-create) を使用し、可用性ゾーンと障害ドメインの両方を使用してホスト グループを作成します。 

```azurecli-interactive
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

`--automatic-placement true` パラメーターを追加すると、VM とスケール セット インスタンスがホスト グループ内のホストに自動的に配置されるようになります。 詳しくは、[手動による配置と自動配置](../dedicated-hosts.md#manual-vs-automatic-placement)に関するページをご覧ください。


### <a name="other-examples"></a>その他の例

[az vm host group create](/cli/azure/vm/host/group#az-vm-host-group-create) を使用して、可用性ゾーン 1 (障害ドメインなし) にホスト グループを作成することもでいます。

```azurecli-interactive
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
以下では、[az vm host group create](/cli/azure/vm/host/group#az-vm-host-group-create) を使用し、障害ドメインのみを使用してホスト グループを作成します (可用性ゾーンがサポートされていないリージョンで使用する場合)。 

```azurecli-interactive
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>ホストを作成する 

次に、ホスト グループに専用ホストを作成してみましょう。 ホストの名前に加えて、ホストの SKU を指定する必要があります。 ホスト SKU では、専用ホストに対してサポートされている VM シリーズとハードウェアの世代がキャプチャされます。  

ホスト SKU の詳細と価格については、「[Azure 専用ホストの価格](https://aka.ms/ADHPricing)」を参照してください。

[az vm host create](/cli/azure/vm/host#az-vm-host-create) を使用してホストを作成します。 ホスト グループの障害ドメイン数を設定した場合は、ホストの障害ドメインを指定するように求められます。  

```azurecli-interactive
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>仮想マシンの作成 
[az vm create](/cli/azure/vm#az-vm-create) を使用して、専用ホスト内に仮想マシンを作成します。 ホスト グループを作成するときに可用性ゾーンを指定した場合は、仮想マシンを作成するときに同じゾーンを使用する必要があります。

```azurecli-interactive
az vm create \
   -n myVM \
   --image debian \
   --host-group myHostGroup \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```

特定のホストに VM を配置するには、`--host-group` でホスト グループを指定するのではなく、`--host` を使用します。
 
> [!WARNING]
> 十分なリソースがないホストに仮想マシンを作成すると、仮想マシンは FAILED 状態で作成されます。 

## <a name="create-a-scale-set"></a>スケール セットを作成する 

スケール セットをデプロイするときは、ホスト グループを指定します。

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --host-group myHostGroup \
  --generate-ssh-keys \
  --size Standard_D4s_v3 \
  -g myDHResourceGroup \
  --zone 1
```

スケール セットをデプロイするホストを手動で選択する場合は、`--host` とそのホストの名前を追加します。


## <a name="check-the-status-of-the-host"></a>ホストの状態を確認する

[az vm host get-instance-view](/cli/azure/vm/host#az-vm-host-get-instance-view) を使用して、ホストの正常性状態と、ホストにデプロイできる仮想マシンの数を確認できます。

```azurecli-interactive
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```
 出力は次のようになります。
 
```json
{
  "autoReplaceOnFailure": true,
  "hostId": "6de80643-0f45-4e94-9a4c-c49d5c777b62",
  "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts/myHost",
  "instanceView": {
    "assetId": "12345678-1234-1234-abcd-abc123456789",
    "availableCapacity": {
      "allocatableVms": [
        {
          "count": 31.0,
          "vmSize": "Standard_D2s_v3"
        },
        {
          "count": 15.0,
          "vmSize": "Standard_D4s_v3"
        },
        {
          "count": 7.0,
          "vmSize": "Standard_D8s_v3"
        },
        {
          "count": 3.0,
          "vmSize": "Standard_D16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-8s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D48s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-16s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-32s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64s_v3"
        }
      ]
    },
    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Provisioning succeeded",
        "level": "Info",
        "message": null,
        "time": "2019-07-24T21:22:40.604754+00:00"
      },
      {
        "code": "HealthState/available",
        "displayStatus": "Host available",
        "level": "Info",
        "message": null,
        "time": null
      }
    ]
  },
  "licenseType": null,
  "location": "eastus2",
  "name": "myHost",
  "platformFaultDomain": 1,
  "provisioningState": "Succeeded",
  "provisioningTime": "2019-07-24T21:22:40.604754+00:00",
  "resourceGroup": "myDHResourceGroup",
  "sku": {
    "capacity": null,
    "name": "DSv3-Type1",
    "tier": null
  },
  "tags": null,
  "type": null,
  "virtualMachines": [
    {
      "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/MYDHRESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/MYVM",
      "resourceGroup": "MYDHRESOURCEGROUP"
    }
  ]
}

```
 
## <a name="export-as-a-template"></a>テンプレートとしてのエクスポート 
同じパラメーターを使用して追加の開発環境を作成する場合や、開発環境に合った運用環境を作成する場合は、テンプレートをエクスポートできます。 リソース マネージャーでは、環境に合ったすべてのパラメーターを定義する JSON テンプレートを使用します。 この JSON テンプレートを参照することで全体の環境を構築します。 JSON テンプレートを手動で構築できます。または、既存の環境をエクスポートして JSON テンプレートを作成することもできます。 [az group export](/cli/azure/group#az-group-export) を使って、リソース グループをエクスポートします。

```azurecli-interactive
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

このコマンドで、現在の作業ディレクトリ内に `myDHResourceGroup.json` ファイルが作成されます。 このテンプレートから環境を作成する場合、リソース名をすべて入力するように求められます。 `az group export` コマンドに `--include-parameter-default-value` パラメーターを追加することで、テンプレート ファイルにこれらの名前を入力できます。 リソース名を指定する JSON テンプレートを編集するか、リソース名を指定する parameters.json ファイルを作成します。
 
テンプレートから環境を作成するには、[az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) を使用します。

```azurecli-interactive
az deployment group create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>クリーンアップ 

仮想マシンがデプロイされていない場合でも、専用ホストに対して課金されます。 コストを節約するには、現在使用していないすべてのホストを削除する必要があります。  

ホストを削除できるのは、それを使用している仮想マシンがなくなった場合のみです。 [az vm delete](/cli/azure/vm#az-vm-delete) を使用して VM を削除します。

```azurecli-interactive
az vm delete -n myVM -g myDHResourceGroup
```

VM を削除した後、[az vm host delete](/cli/azure/vm/host#az-vm-host-delete) を使用してホストを削除できます。

```azurecli-interactive
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
すべてのホストを削除したら、[az vm host group delete](/cli/azure/vm/host/group#az-vm-host-group-delete) を使用してホスト グループを削除できます。  
 
```azurecli-interactive
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
また、1 つのコマンドで、リソース グループ全体を削除することもできます。 これにより、すべての VM、ホスト、ホスト グループを含めて、グループ内に作成されたすべてのリソースが削除されます。
 
```azurecli-interactive
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>次のステップ

- 詳細については、[専用ホスト](../dedicated-hosts.md)の概要に関するページを参照してください。

- また、[Azure portal](../dedicated-hosts-portal.md) を使用して専用ホストを作成することもできます。

- [こちら](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)には、リージョン内の回復性を最大にするためにゾーンと障害ドメインの両方を使用するサンプル テンプレートがあります。
