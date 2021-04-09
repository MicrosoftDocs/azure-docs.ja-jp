---
title: Azure Virtual Machine Scale Sets による自動インスタンス修復
description: スケール セット内の VM インスタンスの自動修復ポリシーを構成する方法について説明します
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 02/28/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: ff67ac4be32142848a12185199d63db5a14e6c34
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501857"
---
# <a name="automatic-instance-repairs-for-azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets の自動インスタンス修復

Azure Virtual Machine Scale Sets の自動インスタンス修復を有効にすると、正常なインスタンスのセットを維持することで、アプリケーションの高可用性を実現できます。 スケール セット内のインスタンスが[アプリケーション正常性拡張機能](./virtual-machine-scale-sets-health-extension.md)または [Load Balancer の正常性プローブ](../load-balancer/load-balancer-custom-probe-overview.md)によって報告された通り異常な状態であることが判明した場合、この機能により問題のあるインスタンスが削除され、新たに作成されたインスタンスにより置き換えられます。

## <a name="requirements-for-using-automatic-instance-repairs"></a>自動インスタンス修復を使用するための要件

**スケール セットでアプリケーションの正常性の監視を有効にする**

スケール セットでは、インスタンスのアプリケーションの正常性の監視が有効になっている必要があります。 これを行うには、[アプリケーション正常性拡張機能](./virtual-machine-scale-sets-health-extension.md)または [Load Balancer の正常性プローブ](../load-balancer/load-balancer-custom-probe-overview.md)を使用します。 この内、有効にできるのは一度に 1 つのみです。 アプリケーション正常性拡張機能または Load Balancer プローブは、仮想マシン インスタンス上に構成されたアプリケーション エンドポイントに ping を行い、アプリケーションの正常性状態を判断します。 この正常性状態は、インスタンスの正常性を監視し、必要に応じて修復を実行するために、スケール セットのオーケストレーターによって使用されます。

**正常性状態を提供するようにエンドポイントを構成する**

自動インスタンス修復ポリシーを有効にする前に、スケール セット インスタンスにアプリケーションの正常性状態を出力するように構成されたアプリケーション エンドポイントがあることを確認します。 インスタンスからこのアプリケーション エンドポイントに対して状態 200 (OK) が返されると、そのインスタンスは "正常" とマークされます。 その他のすべての場合、次のシナリオを含め、インスタンスは "異常" とマークされます。

- 仮想マシン インスタンス内に、アプリケーションの正常性状態を提供するアプリケーション エンドポイントが構成されていない場合
- アプリケーション エンドポイントが正しく構成されていない場合
- アプリケーション エンドポイントに到達できない場合

"異常" とマークされているインスタンスの場合、自動修復はスケール セットによってトリガーされます。 エンドポイントの構成中に予期せずインスタンスが修復されることを回避するため、アプリケーション エンドポイントが正しく構成されていることを確認してから、自動修復ポリシーを有効にします。

**スケール セット内のインスタンスの最大数**

この機能は現在、インスタンスの数が 200 個以下のスケール セットに対してのみ使用できます。 スケール セットは、1 つの配置グループまたは複数の配置グループとしてデプロイできます。ただし、スケール セットに対して自動インスタンス修復が有効になっている場合、インスタンス数は 200 を超えることはできません。

**API バージョン**

自動修復ポリシーは、コンピューティング API バージョン 2018-10-01 以降でサポートされています。

**リソースまたはサブスクリプションの移動に関する制限事項**

自動修復機能が有効になっている場合、スケール セットでは、リソースやサブスクリプションの移動は現在サポートされていません。

**Service Fabric スケール セットの制限事項**

現在、この機能は Service Fabric スケール セットではサポートされていません。

## <a name="how-do-automatic-instance-repairs-work"></a>自動インスタンス修復のしくみ

自動インスタンス修復機能は、スケール セット内の個々のインスタンスの正常性の監視に依存します。 スケール セット内の VM インスタンスは、[アプリケーション正常性拡張機能](./virtual-machine-scale-sets-health-extension.md)または [Load Balancer の正常性プローブ](../load-balancer/load-balancer-custom-probe-overview.md)を使用してアプリケーションの正常性状態を出力するように構成できます。 インスタンスが異常であることが判明した場合、スケール セットでは正常でないインスタンスが削除されて新たに作成されたインスタンスにより置き換えられ、修復操作が実行されます。 新しいインスタンスの作成には、最新の仮想マシン スケール セット モデルが使用されます。 この機能は、*automaticRepairsPolicy* オブジェクトを使用して、仮想マシン スケール セット モデルで有効にすることができます。

### <a name="batching"></a>バッチ処理

自動インスタンス修復操作は、バッチで実行されます。 どの時点においても、スケール セット内のインスタンスの 5% 以上が自動修復ポリシーを使用して修復されることはありません。 これにより、同時に異常が検出された場合に、多数のインスタンスが同時に削除され、再作成されるのを防ぐことができます。

### <a name="grace-period"></a>猶予期間

スケール セットで実行される PUT、PATCH、または POST アクション (再イメージ化、再デプロイ、更新など) が原因でインスタンスの状態変更操作が開始されると、そのインスタンスの修復操作は、猶予期間が経過した後にのみ実行されます。 猶予期間は、インスタンスが正常な状態に戻るまでにかけられる期間です。 猶予期間は、状態の変更が完了した後に開始されます。 これは、修復操作が不完全または偶発的に実行されることを回避するのに役立ちます。 猶予期間は、スケール セット内に新しく作成されたインスタンス (修復操作の結果として作成されたインスタンスを含む) に適用されます。 猶予期間は、ISO 8601 形式では分単位で指定し、プロパティ *automaticRepairsPolicy.gracePeriod* を使用して設定できます。 猶予期間は 30 分から 90 分の範囲で指定でき、既定値は 30 分です。

### <a name="suspension-of-repairs"></a>修復の中断 

仮想マシン スケール セットでは、必要に応じて自動インスタンス修復を一時的に中断する機能が提供されます。 仮想マシン スケール セットのインスタンス ビューのプロパティ *orchestrationServices* の下にある自動修復の *serviceState* には、自動修復の現在の状態が表示されます。 スケール セットが自動修復にオプトインされると、パラメーター *serviceState* の値が *Running* に設定されます。 スケール セットの自動修復が中断されると、パラメーター *serviceState* は *Suspended* に設定されます。 スケール セットに *automaticRepairsPolicy* が定義されていても、自動修復機能が有効になっていない場合、パラメーター *serviceState* は *Not Running* に設定されます。

修復操作を繰り返し実行した後も、スケール セット内の異常なものを置き換えるために新しく作成されたインスタンスが異常な状態のままになる場合、安全対策として、プラットフォームでは自動修復の *serviceState* が *Suspended* に更新されます。 自動修復の *serviceState* の値を *Running* に設定することで、もう一度自動修復を再開することができます。 詳細な手順については、スケール セットの[自動修復ポリシーのサービス状態の表示と更新](#viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy)に関するセクションを参照してください。 

自動インスタンス修復プロセスは、次のように機能します。

1. [アプリケーション正常性拡張機能](./virtual-machine-scale-sets-health-extension.md)または [Load Balancer の正常性プローブ](../load-balancer/load-balancer-custom-probe-overview.md)は、スケール セット内の各仮想マシンのアプリケーション エンドポイントに ping を行って、各インスタンスのアプリケーションの正常性状態を取得します。
2. エンドポイントがステータス 200 (OK) で応答した場合、インスタンスは "正常" とマークされます。 それ以外のすべての場合 (エンドポイントに到達できない場合を含む)、インスタンスは "異常" とマークされます。
3. インスタンスが異常であることが判明すると、スケール セットでは正常でないインスタンスが削除されて新たに作成されたインスタンスにより置き換えられ、修復操作がトリガされます。
4. インスタンスの修復はバッチで実行されます。 どの時点においても、スケール セット内のインスタンスの合計の 5% 以上が修復されることはありません。 スケール セットのインスタンス数が 20 未満の場合、異常なインスタンスの修復は一度に 1 つずつ実行されます。
5. 上記のプロセスは、スケール セット内のすべての異常なインスタンスが修復されるまで続行されます。

## <a name="instance-protection-and-automatic-repairs"></a>インスタンスの保護と自動修復

スケール セット内のインスタンスが[保護ポリシー](./virtual-machine-scale-sets-instance-protection.md)のいずれかを適用することで保護されている場合、そのインスタンスに対して自動修復は実行されません。 これは、次の両方の保護ポリシーに適用されます: "*スケールインから保護する*" および "*スケールセットから保護する*" アクション。 

## <a name="terminatenotificationandautomaticrepairs"></a>終了通知と自動修復

スケール セットで[終了通知](./virtual-machine-scale-sets-terminate-notification.md)機能が有効になっている場合、自動修復操作中の異常なインスタンスの削除は、終了通知構成に従って行われます。 終了通知は、Azure メタデータ サービス (スケジュールされたイベント) を介して送信され、構成された遅延タイムアウトの間、インスタンスの削除が遅延します。 しかし、異常なものを置き換えるための新しいインスタンスの作成では、遅延タイムアウトが完了するまで待機しません。

## <a name="enabling-automatic-repairs-policy-when-creating-a-new-scale-set"></a>新しいスケール セットを作成するときに自動修復ポリシーを有効にする

新しいスケール セットの作成時に自動修復ポリシーを有効にするには、この機能をオプトインするためのすべての[要件](#requirements-for-using-automatic-instance-repairs)が満たされていることを確認します。 エンドポイントの構成中に意図せず修復がトリガーされないように、スケール セット インスタンスのアプリケーション エンドポイントを正しく構成する必要があります。 新しく作成されたスケール セットの場合、すべてのインスタンスの修復は、猶予期間が終了するまで待機した後に実行されます。 スケール セットで自動インスタンス修復を有効にするには、仮想マシン スケール セット モデルで *automaticRepairsPolicy* オブジェクトを使用します。

また、この[クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-automatic-repairs-slb-health-probe)を使用して、ロード バランサーの正常性プローブと自動インスタンス修復を 30 分の猶予期間で有効にした仮想マシン スケール セットをデプロイすることもできます。

### <a name="azure-portal"></a>Azure portal
 
次の手順では、新しいスケール セットを作成するときに自動修復ポリシーを有効にします。
 
1. **[仮想マシン スケール セット]** に移動します。
1. **[+ 追加]** を選択して、新しいスケール セットを作成します。
1. **[正常性]** タブに移動します。 
1. **[正常性]** セクションを見つけます。
1. **[アプリケーションの正常性を監視する]** オプションを有効にします。
1. **[自動修復ポリシー]** セクションを見つけます。
1. **[自動修復]** オプションを **[オン]** にします。
1. **[Grace period (min)]\(猶予期間 (分)\)** で、猶予期間を分単位で指定します。指定できる値は 30 分から 90 分です。 
1. 新しいスケール セットの作成が完了したら、 **[確認と作成]** ボタンを選択します。

### <a name="rest-api"></a>REST API

次の例では、スケール セット モデルで自動インスタンス修復を有効にする方法を示します。 API バージョン 2018-10-01 以降をお使いください。

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT30M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

新しいスケール セットの作成時に自動インスタンス修復機能を有効にするには、[New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) コマンドレットを使用します。 このサンプル スクリプトでは、構成ファイルを使用したスケール セットと関連リソースの作成について説明します。[完全な仮想マシン スケール セットを作成します](./scripts/powershell-sample-create-complete-scale-set.md)。 自動インスタンス修復ポリシーを構成するには、パラメーター *EnableAutomaticRepair* と *AutomaticRepairGracePeriod* をスケール セットを作成するための構成オブジェクトに追加します。 次の例では、30 分の猶予期間でこの機能を有効にします。

```azurepowershell-interactive
New-AzVmssConfig `
 -Location "EastUS" `
 -SkuCapacity 2 `
 -SkuName "Standard_DS2" `
 -UpgradePolicyMode "Automatic" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT30M"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

次の例では、 *[az vmss create](/cli/azure/vmss#az-vmss-create)* を使用して新しいスケール セットを作成するときに、自動修復ポリシーを有効にします。 まず、リソース グループを作成してから、自動修復ポリシーの猶予期間を 30 分に設定した新しいスケール セットを作成します。

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --load-balancer <existingLoadBalancer> \
  --health-probe <existingHealthProbeUnderLoaderBalancer> \
  --automatic-repairs-grace-period 30
```

上の例では、既存のロード バランサーと正常性プローブを使用して、インスタンスのアプリケーションの正常性状態を監視します。 代わりに、アプリケーションの正常性拡張機能を使用して監視する場合は、スケール セットを作成し、アプリケーションの正常性拡張機能を構成してから、次のセクションで説明されているように *az vmss update* を使用して自動インスタンス修復ポリシーを有効にすることができます。

## <a name="enabling-automatic-repairs-policy-when-updating-an-existing-scale-set"></a>既存のスケール セットを更新するときに自動修復ポリシーを有効にする

既存のスケール セットで自動修復ポリシーを有効にする前に、この機能をオプトインするためのすべての[要件](#requirements-for-using-automatic-instance-repairs)が満たされていることを確認してください。 エンドポイントの構成中に意図せず修復がトリガーされないように、スケール セット インスタンスのアプリケーション エンドポイントを正しく構成する必要があります。 スケール セットで自動インスタンス修復を有効にするには、仮想マシン スケール セット モデルで *automaticRepairsPolicy* オブジェクトを使用します。

既存のスケール セットのモデルを更新した後、最新のモデルが確実にスケールのすべてのインスタンスに適用されるようにします。 手順については、「[最新のスケール セット モデルで VM を最新の状態にする方法](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)」を参照してください。

### <a name="azure-portal"></a>Azure portal

Azure portal を使用して、既存のスケール セットの自動修復ポリシーを変更することができます。 
 
1. 既存の仮想マシン スケール セットに移動します。
1. 左側のメニューの **[設定]** で、 **[正常性と修復]** を選択します。
1. **[アプリケーションの正常性を監視する]** オプションを有効にします。
1. **[自動修復ポリシー]** セクションを見つけます。
1. **[自動修復]** オプションを **[オン]** にします。
1. **[Grace period (min)]\(猶予期間 (分)\)** で、猶予期間を分単位で指定します。指定できる値は 30 分から 90 分です。 
1. 完了したら、 **[保存]** を選択します。 

### <a name="rest-api"></a>REST API

次の例では、猶予期間が 40 分のポリシーを有効にします。 API バージョン 2018-10-01 以降をお使いください。

```
PUT or PATCH on '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-07-01'
```

```json
{
  "properties": {
    "automaticRepairsPolicy": {
            "enabled": "true",
            "gracePeriod": "PT40M"
        }
    }
}
```

### <a name="azure-powershell"></a>Azure PowerShell

[Update-AzVmss](/powershell/module/az.compute/update-azvmss) コマンドレットを使用して、既存のスケール セットの自動インスタンス修復機能の構成を変更します。 次の例では、猶予期間を 40 分に更新します。

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -EnableAutomaticRepair $true `
 -AutomaticRepairGracePeriod "PT40M"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

*[az vmss update](/cli/azure/vmss#az-vmss-update)* を使用して、既存のスケール セットの自動インスタンス修復ポリシーを更新する例を次に示します。

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-automatic-repairs true \
  --automatic-repairs-grace-period 30
```

## <a name="viewing-and-updating-the-service-state-of-automatic-instance-repairs-policy"></a>自動インスタンス修復ポリシーのサービス状態の表示と更新

### <a name="rest-api"></a>REST API 

仮想マシン スケール セットで API バージョン 2019-12-01 以降を指定して「[インスタンス ビューの取得](/rest/api/compute/virtualmachinescalesets/getinstanceview)」を使用し、プロパティ *orchestrationServices* の下の自動修復の *serviceState* を表示します。 

```http
GET '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version=2019-12-01'
```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Running"
    }
  ]
}
```

仮想マシン スケール セットで API バージョン 2019-12-01 以降を指定して *setOrchestrationServiceState* API を使用し、自動修復の状態を設定します。 スケール セットが自動修復機能にオプトインされたら、この API を使用して、スケール セットの自動修復を中断または再開できます。 

 ```http
 POST '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/setOrchestrationServiceState?api-version=2019-12-01'
 ```

```json
{
  "orchestrationServices": [
    {
      "serviceName": "AutomaticRepairs",
      "serviceState": "Suspend"
    }
  ]
}
```

### <a name="azure-cli"></a>Azure CLI 

[get-instance-view](/cli/azure/vmss#az-vmss-get-instance-view) コマンドレットを使用して、自動インスタンス修復の *serviceState* を表示します。 

```azurecli-interactive
az vmss get-instance-view \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```

[set-orchestration-service-state](/cli/azure/vmss#az-vmss-set-orchestration-service-state) コマンドレットを使用して、自動インスタンス修復の *serviceState* を更新します。 スケール セットが自動修復機能にオプトインされたら、このコマンドレットを使用して、スケール セットの自動修復を中断または再開できます。 

```azurecli-interactive
az vmss set-orchestration-service-state \
    --service-name AutomaticRepairs \
    --action Resume \
    --name MyScaleSet \
    --resource-group MyResourceGroup
```
### <a name="azure-powershell"></a>Azure PowerShell

パラメーター *InstanceView* を指定して [Get-AzVmss](/powershell/module/az.compute/get-azvmss) コマンドレットを使用し、自動インスタンス修復の *ServiceState* を表示します。

```azurepowershell-interactive
Get-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -InstanceView
```

Set-AzVmssOrchestrationServiceState コマンドレットを使用して、自動インスタンス修復の *serviceState* を更新します。 スケール セットが自動修復機能にオプトインされたら、このコマンドレットを使用して、スケール セットの自動修復を中断または再開できます。

```azurepowershell-interactive
Set-AzVmssOrchestrationServiceState `
    -ResourceGroupName "myResourceGroup" `
    -VMScaleSetName "myScaleSet" `
    -ServiceName "AutomaticRepairs" `
    -Action "Suspend"
```

## <a name="troubleshoot"></a>トラブルシューティング

**自動修復ポリシーを有効にできない**

"'プロパティ' 型のオブジェクトにメンバー 'automaticRepairsPolicy' が見つかりませんでした" というメッセージが表示された 'BadRequest' エラーが発生した場合は、仮想マシン スケール セットに使用されている API バージョンを確認してください。 この機能では、API バージョン 2018-10-01 以降が必要です。

**ポリシーが有効にもかかわらずインスタンスが修復されない**

インスタンスの猶予期間内である可能性があります。 これは、インスタンスの状態が変更されてから、修復が実行されるまでの待機時間です。 これは、修復が不完全または偶発的に実行されることを回避するためです。 修復操作は、インスタンスの猶予期間が完了した後に実行されます。

**スケール セット インスタンスのアプリケーションの正常性状態の表示**

仮想マシンスケール セットのインスタンスに対して [Get Instance View API](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) を使用すると、アプリケーションの正常性状態を表示できます。 Azure PowerShell では、コマンドレット [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) を *InstanceView* フラグと共に使用できます。 アプリケーションの正常性状態は、プロパティ *vmHealth* に表示されます。

Azure portal では、正常性状態も確認できます。 既存のスケール セットに移動し、左側のメニューから **[インスタンス]** を選択し、 **[正常性状態]** 列で各スケール セット インスタンスの正常性状態を確認します。 

## <a name="next-steps"></a>次のステップ

スケール セットの [アプリケーション正常性拡張機能](./virtual-machine-scale-sets-health-extension.md) または [Load Balancer の正常性プローブ](../load-balancer/load-balancer-custom-probe-overview.md) を構成する方法について説明します。
