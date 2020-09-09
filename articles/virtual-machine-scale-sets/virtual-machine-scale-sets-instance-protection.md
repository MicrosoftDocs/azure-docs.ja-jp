---
title: Azure 仮想マシン スケール セット インスタンスのインスタンス保護
description: Azure 仮想マシン スケール セット インスタンスをスケールインおよびスケールセット操作から保護する方法について説明します。
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 02/26/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: 8c4944da8ffcaa75e6448483918a29809c32830b
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124059"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Azure 仮想マシン スケール セット インスタンスのインスタンス保護

Azure 仮想マシン スケール セットを使用すると、[自動スケーリング](virtual-machine-scale-sets-autoscale-overview.md)によるワークロードの弾力性が向上するので、インフラストラクチャのスケールアウトとスケールインのタイミングを構成できます。 また、スケール セットを使用すると、さまざまな[アップグレード ポリシー](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)設定を介して多数の VM の管理、構成、および更新を一元的に行うこともできます。 アップグレード ポリシーを [自動] または [ローリング] に設定すると、スケール セット モデル上で更新プログラムを構成して、新しい構成がすべてのスケール セット インスタンスに自動的に適用されるようにすることができます。

アプリケーションでトラフィックが処理されるときに、特定のインスタンスを他のスケール セット インスタンスとは異なる方法で処理することができます。 たとえば、スケール セット内の特定のインスタンスで実行時間の長い操作が実行されている可能性があるため、操作が完了するまではこのようなインスタンスをスケールインしないでください。 また、追加のタスクまたはスケール セット内の他のメンバーとは別のタスクを実行するために、スケール セット内のいくつかのインスタンスを特殊化した場合もあります。 スケール セット内の他のインスタンスではこれらの "特殊な" VM を変更しないでください。 インスタンス保護には、アプリケーションでこれらのシナリオやその他のシナリオを有効にする追加のコントロールが用意されています。

この記事では、スケール セット インスタンスにさまざまなインスタンス保護機能を適用および使用する方法について説明します。

## <a name="types-of-instance-protection"></a>インスタンス保護の種類
スケール セットには、2 種類のインスタンス保護機能があります。

-   **スケールインから保護する**
    - スケール セット インスタンスの **protectFromScaleIn** プロパティを使用して有効になります
    - 自動スケーリングで開始されたスケールインからインスタンスを保護します
    - ユーザーが開始したインスタンス操作 (インスタンスの削除を含む) は**ブロックされません**
    - スケール セットで開始された操作 (アップグレード、再イメージ化、割り当て解除など) は**ブロックされません**

-   **スケール セット アクションから保護する**
    - スケール セット インスタンスの **protectFromScaleSetActions** プロパティを使用して有効になります
    - 自動スケーリングで開始されたスケールインからインスタンスを保護します
    - スケール セットで開始された操作 (アップグレード、再イメージ化、割り当て解除など) からインスタンスを保護します
    - ユーザーが開始したインスタンス操作 (インスタンスの削除を含む) は**ブロックされません**
    - 完全なスケール セットの削除は**ブロックされません**

## <a name="protect-from-scale-in"></a>スケールインから保護する
インスタンス保護は、インスタンスが作成された後にスケール セット インスタンスに適用できます。 保護の適用と変更は[インスタンス モデル](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view)に対してのみ行われ、[スケール セット モデル](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)に対しては行われません。

次の例で詳しく示すように、スケール セット インスタンスにスケールインの保護を適用する方法は複数あります。

### <a name="azure-portal"></a>Azure portal

Azure portal を使用して、スケールイン保護をスケール セット内のインスタンスに適用できます。 一度に複数のインスタンスを調整することはできません。 保護するインスタンスごとにこの手順を繰り返します。
 
1. 既存の仮想マシン スケール セットに移動します。
1. 左側の **[設定]** のメニューから **[インスタンス]** を選択します。
1. 保護するインスタンスの名前を選択します。
1. **[保護ポリシー]** タブを選択します。
1. **[保護ポリシー]** ブレードで、 **[スケールインから保護する]** オプションを選択します。
1. **[保存]** を選択します。 

### <a name="rest-api"></a>REST API

次の例では、スケール セット内のインスタンスにスケールイン保護を適用します。

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true
    }
  }        
}

```

> [!NOTE]
>インスタンス保護は、API バージョン 2019-03-01 以降でのみサポートされています

### <a name="azure-powershell"></a>Azure PowerShell

スケール セット インスタンスにスケールイン保護を適用するには、[Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) コマンドレットを使用します。

次の例では、インスタンス ID が 0 に設定されたスケール セット内のインスタンスにスケールイン保護を適用します。

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

スケール セット インスタンスにスケールイン保護を適用するには、[az vmss update](/cli/azure/vmss#az-vmss-update) を使用します。

次の例では、インスタンス ID が 0 に設定されたスケール セット内のインスタンスにスケールイン保護を適用します。

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>スケール セット アクションから保護する
インスタンス保護は、インスタンスが作成された後にスケール セット インスタンスに適用できます。 保護の適用と変更は[インスタンス モデル](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view)に対してのみ行われ、[スケール セット モデル](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)に対しては行われません。

インスタンスをスケール セット アクションから保護すると、インスタンスは自動スケーリングで開始されたスケールインからも保護されます。

次の例で詳しく示すように、スケール セット インスタンスにスケール セット アクション保護を適用する方法は複数あります。

### <a name="azure-portal"></a>Azure portal

Azure portal を使用して、スケール セット アクションからの保護をスケール セット内のインスタンスに適用できます。 一度に複数のインスタンスを調整することはできません。 保護するインスタンスごとにこの手順を繰り返します。
 
1. 既存の仮想マシン スケール セットに移動します。
1. 左側の **[設定]** のメニューから **[インスタンス]** を選択します。
1. 保護するインスタンスの名前を選択します。
1. **[保護ポリシー]** タブを選択します。
1. **[保護ポリシー]** ブレードで、 **[スケール セット アクションから保護する]** オプションを選択します。
1. **[保存]** を選択します。 

### <a name="rest-api"></a>REST API

次の例では、スケール セット アクションからの保護をスケール セット内のインスタンスに適用します。

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vMScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true,
      "protectFromScaleSetActions": true
    }
  }        
}

```

> [!NOTE]
>インスタンス保護は、API バージョン 2019-03-01 以降でのみサポートされています。</br>
インスタンスをスケール セット アクションから保護すると、インスタンスは自動スケーリングで開始されたスケールインからも保護されます。 "protectFromScaleSetActions": true を設定する場合は "protectFromScaleIn": false を指定できません。

### <a name="azure-powershell"></a>Azure PowerShell

スケール セット アクションからスケール セット インスタンスに保護を適用するには、[Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) コマンドレットを使用します。

次の例では、インスタンス ID 0 のスケール セット内のインスタンスにスケール セット アクションからの保護を適用します。

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

スケール セット アクションからの保護をスケール セット インスタンスに適用するには、[az vmss update](/cli/azure/vmss#az-vmss-update) を使用します。

次の例では、インスタンス ID 0 のスケール セット内のインスタンスにスケール セット アクションからの保護を適用します。

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>トラブルシューティング
### <a name="no-protectionpolicy-on-scale-set-model"></a>スケール セット モデル対して protectionPolicy がない
インスタンス保護は、スケール セット インスタンスにのみ適用され、スケール セット モデルには適用されません。

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>スケール セット インスタンス モデルに対して protectionPolicy がない
既定では、保護ポリシーは作成時にインスタンスに適用されません。

インスタンスの作成後、インスタンス保護をスケール セット インスタンスに適用できます。

### <a name="not-able-to-apply-instance-protection"></a>インスタンス保護を適用できない
インスタンス保護は、API バージョン 2019-03-01 以降でのみサポートされています。 使用されている API のバージョンを確認し、必要に応じて更新します。 必要に応じて、PowerShell または CLI を最新バージョンに更新します。

## <a name="next-steps"></a>次のステップ
仮想マシン スケール セットに[ご自身のアプリケーションをデプロイする](virtual-machine-scale-sets-deploy-app.md)方法を学習します。
