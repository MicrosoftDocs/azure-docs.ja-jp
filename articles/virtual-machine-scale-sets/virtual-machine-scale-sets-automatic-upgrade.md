---
title: Azure 仮想マシン スケール セットを使用した OS イメージの自動アップグレード | Microsoft Docs
description: スケール セット内の VM インスタンス上の OS イメージを自動的にアップグレードする方法について説明します
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: manayar
ms.openlocfilehash: 007f2801efed8da4964808056563418dec7f64d5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798510"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Azure 仮想マシン スケール セットによる OS イメージの自動アップグレード

スケール セットでの自動 OS イメージ アップグレードを有効にすると、スケール セット内のすべてのインスタンスの OS ディスクが安全かつ自動的にアップグレードされるようになり、更新プログラムの管理が簡素化されます。

OS の自動アップグレードには、次の特徴があります。

- 構成すると、イメージ発行元によって公開された最新の OS イメージが、ユーザーの介入なしでスケール セットに自動的に適用されます。
- 新しいプラットフォーム イメージが発行元によって発行されるたびに、ローリング方法でバッチごとにインスタンスをアップグレードします。
- アプリケーション正常性プローブおよび[アプリケーション正常性拡張機能](virtual-machine-scale-sets-health-extension.md)と統合されます。
- すべての VM サイズ、および Windows と Linux の両方のプラットフォーム イメージで動作します。
- 自動アップグレードはいつでも停止できます (OS のアップグレードは、手動でも開始できます)。
- VM の OS ディスクが、最新バージョンのイメージで作成された新しい OS ディスクに置き換えられます。 永続化されているデータ ディスクは保持されたまま、構成済みの拡張機能とカスタム データ スクリプトが実行されます。
- [拡張機能のシーケンス処理](virtual-machine-scale-sets-extension-sequencing.md)がサポートされています。

## <a name="how-does-automatic-os-image-upgrade-work"></a>OS イメージの自動アップグレードのしくみ

アップグレードは、VM の OS ディスクを、最新のイメージ バージョンを使用して作成された新しいディスクに置き換えることによって実行されます。 永続化されているデータ ディスクは保持されたまま、すべての構成済みの拡張機能とカスタム データ スクリプトが OS ディスク上で実行されます。 アプリケーションのダウンタイムを最小限に抑えるため、アップグレードはバッチで行われ、いつでもスケール セットの 20% を超えてアップグレードされることはありません。 また、Azure Load Balancer アプリケーション正常性プローブや[アプリケーション正常性拡張機能](virtual-machine-scale-sets-health-extension.md)を統合することもできます。 アプリケーションのハートビートを組み込み、アップグレード プロセスでのバッチごとのアップグレードの成功を検証することをお勧めします。

アップグレード プロセスは次のように実行されます。
1. アップグレード プロセスを開始する前に、オーケストレーターは異常なインスタンスが全体的なスケール セットの 20% を超えていないことを確認します。
2. アップグレード オーケストレーターは、アップグレードする VM インスタンスのバッチ (インスタンスの合計数の最大 20% を含む 1 つのバッチ) を識別します。
3. 選択された VM インスタンス バッチの OS ディスクが、最新のイメージから作成された新しい OS ディスクに置き換えられ、スケール セット モデル内で指定されたすべての拡張機能と構成が、アップグレード済みのインスタンスに適用されます。
4. 構成済みのアプリケーション正常性プローブやアプリケーション正常性拡張機能があるスケール セットの場合、アップグレードはインスタンスが正常になるまで 5 分間待機した後、次のバッチのアップグレードに進みます。
5. また、アップグレード オーケストレーターでは、アップグレード後に異常が発生したインスタンスの割合も追跡されます。 アップグレード処理中に異常なアップグレード済みインスタンスの割合が 20% を超えた場合、アップグレードは停止します。
6. 上記のプロセスは、スケール セット内のすべてのインスタンスがアップグレードされるまで続行されます。

スケール セットの OS アップグレード オーケストレーターは、各バッチをアップグレードする前に、スケール セットの全体の正常性を確認します。 バッチのアップグレード中には、他の計画済みまたは計画外メンテナンスが同時に発生することがあり、それによってスケール セット インスタンスの正常性が影響を受ける場合があります。 そのような場合、スケール セットのインスタンスの 20% 以上が異常な状態になると、スケール セットのアップグレードは現在のバッチが終了した時点で停止します。

## <a name="supported-os-images"></a>サポート対象の OS イメージ
現時点では、特定の OS プラットフォーム イメージのみがサポートされています。 カスタム イメージは現時点ではサポートされていません。

現時点では、次の SKU がサポートされています (定期的に追加される予定です)。

| Publisher               | OS 製品      |  Sku               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04 LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS          |
| Rogue Wave (OpenLogic)  | CentOS        | 7.5                |
| CoreOS                  | CoreOS        | 安定版             |
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter    |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-with-Containers |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-with-Containers |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>OS イメージの自動アップグレードを構成するための要件

- プラットフォーム イメージの *version* プロパティには *latest* を設定する必要があります。
- Service Fabric 以外のスケール セットには、アプリケーション正常性プローブまたは[アプリケーション正常性拡張機能](virtual-machine-scale-sets-health-extension.md)を使用します。
- スケール セット モデルで指定された外部リソースが利用可能であり、更新可能であることを確認してください。 例としては、VM 拡張プロパティ内のブートストラップ ペイロードの SAS URI、ストレージ アカウント内のペイロード、モデル内のシークレットへの参照などが挙げられます。

## <a name="configure-automatic-os-image-upgrade"></a>OS イメージの自動アップグレードの構成
OS イメージの自動アップグレードを構成するには、スケール セットのモデル定義で *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* プロパティが *true* に設定されていることを確認します。

### <a name="rest-api"></a>REST API
次の例は、スケール セット モデルでの自動 OS アップグレードを設定する方法について説明したものです。

```
PUT or PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version=2018-10-01`
```

```json
{
  "properties": {
    "upgradePolicy": {
      "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade":  true
      }
    }
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
[Update AzVmss](/powershell/module/az.compute/update-azvmss) コマンドレットを使用して、スケール セットの OS アップグレード履歴を確認します。 次の例では、*myResourceGroup* というリソース グループ内の *myVMSS* というスケール セットの自動アップグレードを構成しています。

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
[az vmss update](/cli/azure/vmss#az-vmss-update) を使用して、スケール セットの OS アップグレード履歴を確認します。 Azure CLI 2.0.47 以上を使用してください。 次の例では、*myResourceGroup* というリソース グループ内の *myVMSS* というスケール セットの自動アップグレードを構成しています。

```azurecli-interactive
az vmss update --name myVMSS --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

## <a name="using-application-health-probes"></a>アプリケーションの正常性プローブの使用

OS のアップグレード中は、スケール セット内の VM インスタンスが、一度に 1 つのバッチでアップグレードされます。 アップグレードは、アップグレード済みの VM インスタンス上でユーザーのアプリケーションが正常である場合のみ続行されます。 アプリケーションがスケール セットの OS アップグレード エンジンに正常性通知を提供することをお勧めします。 既定では、OS のアップグレード中、プラットフォームは、VM の電源状態と拡張機能のプロビジョニング状態を考慮して、アップグレード後に VM インスタンスが正常であるかどうかを判断します。 VM インスタンスの OS のアップグレード中、VM インスタンス上の OS ディスクは、最新バージョンのイメージに基づく新しいディスクに置き換えられます。 OS のアップグレードが完了した後、構成済みの拡張機能がこれらの VM 上で実行されます。 アプリケーションは、インスタンス上のすべての拡張機能が正常にプロビジョニングされた場合にのみ、正常であるとみなされます。

スケール セットにアプリケーション正常性プローブをオプションで構成して、アプリケーションの進行中の状態に関する正確な情報をプラットフォームに提供できます。 アプリケーション正常性プローブは、正常性シグナルとして使用されるカスタム ロード バランサー プローブです。 スケール セットの VM インスタンスで実行されているアプリケーションは、外部 HTTP または TCP 要求に応答して、正常かどうかを示すことができます。 カスタム ロード バランサー プローブの動作方法の詳細については、「[Load Balancer プローブを理解する](../load-balancer/load-balancer-custom-probe-overview.md)」を参照してください。 アプリケーション正常性プローブは、Service Fabric スケール セットに対して必須ではありませんが、使用することをお勧めします。 Service Fabric 以外のスケール セットでは、Load Balancer アプリケーション正常性プローブまたは[アプリケーション正常性拡張機能](virtual-machine-scale-sets-health-extension.md)が必須となります。

スケール セットが複数の配置グループを使用するように構成されている場合は、[Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) を使用するプローブを使用する必要があります。

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>スケール セットに関するアプリケーション正常性プローブとしてのカスタム ロード バランサー プローブの構成
ベスト プラクティスとして、スケール セットの正常性のためのロード バランサー プローブを明示的に作成します。 既存の HTTP プローブまたは TCP プローブと同じエンドポイントを使用できますが、この正常性プローブでは、従来のロード バランサー プローブとは異なる動作が必要になる可能性があります。 たとえば、従来のロード バランサー プローブは、インスタンスの負荷が高すぎる場合に異常を返すことがありますが、OS の自動アップグレード中にインスタンスの正常性を決定するには、その動作は適切ではない可能性があります。 2 分未満のプローブ率が高いプローブを構成します。

ロード バランサー プローブは、スケール セットの *networkProfile* 内で参照でき、次のように、内部または公開されているロード バランサーに関連付けることができます。

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
}
```

> [!NOTE]
> Service Fabric と OS 自動アップグレードを使用している場合、Service Fabric で実行されているサービスの高可用性を維持するために、新しい OS イメージは更新ドメインごとにロールアウトされます。 Service Fabric で OS 自動アップグレードを利用するには、クラスターが、Silver 以上の耐久性レベルを使用するように構成されている必要があります。 Service Fabric クラスターの持続性の特徴の詳細については、[こちらのドキュメント](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)を参照してください。

### <a name="keep-credentials-up-to-date"></a>資格情報を最新に保つ
スケール セットが外部のリソースにアクセスするときに資格情報を使用する場合、たとえば、VM 拡張機能がストレージ アカウントの SAS トークンを使用するように構成されている場合には、資格情報が最新であることを確認してください。 証明書やトークンなどの資格情報の期限が切れている場合は、アップグレードは失敗し、VM の最初のバッチは障害が発生した状態になります。

リソースの認証エラーが発生した場合に、VM を復旧し、OS の自動アップグレードを再度有効にするための推奨手順は次のとおりです。

* 拡張機能に渡されたトークン (またはその他の資格情報) を再生成します。
* 外部エンティティと対話する VM 内から使用されるすべての資格情報が最新であることを確認します。
* 新しいトークンで、スケール セット モデルの拡張機能を更新します。
* 障害が発生したものを含むすべての VM インスタンスを更新する、更新されたスケール セットをデプロイします。

## <a name="using-application-health-extension"></a>アプリケーション正常性拡張機能の使用
アプリケーションの正常性拡張機能は、仮想マシン スケール セットのインスタンス内にデプロイされ、スケール セット インスタンス内から VM の正常性についてレポートします。 拡張機能は、アプリケーション エンドポイントでプローブを実行し、そのインスタンスでアプリケーションの状態を更新するように構成することができます。 このインスタンスの状態は Azure によってチェックされ、インスタンスがアップグレード操作の対象となるかどうかが判断されます。

拡張機能は VM 内から正常性をレポートするため、(カスタム Azure Load Balancer [プローブ](../load-balancer/load-balancer-custom-probe-overview.md)を使用する) アプリケーションの正常性プローブなどの外部プローブが使用できない状況で使用できます。

[こちらの記事](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension)の例で詳しく示すように、アプリケーションの正常性拡張機能をお使いのスケール セットにデプロイする方法は複数あります。

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>OS イメージの自動アップグレードの履歴を取得する
Azure PowerShell、Azure CLI 2.0、または REST API を使用して、スケール セットに対して実行された最新の OS のアップグレードの履歴を確認できます。 過去 2 か月間に試行された最後の 5 回の OS アップグレードの履歴を取得できます。

### <a name="rest-api"></a>REST API
次の例では、[REST API](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) を使用して、*myResourceGroup* という名前のリソース グループ内の *myVMSS* という名前のスケール セットの状態をチェックします。

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2018-10-01`
```

GET 呼び出しは、次の例の出力に似たプロパティを返します。

```json
{
    "value": [
        {
            "properties": {
        "runningStatus": {
          "code": "RollingForward",
          "startTime": "2018-07-24T17:46:06.1248429+00:00",
          "completedTime": "2018-04-21T12:29:25.0511245+00:00"
        },
        "progress": {
          "successfulInstanceCount": 16,
          "failedInstanceCount": 0,
          "inProgressInstanceCount": 4,
          "pendingInstanceCount": 0
        },
        "startedBy": "Platform",
        "targetImageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "2016.127.20180613"
        },
        "rollbackInfo": {
          "successfullyRolledbackInstanceCount": 0,
          "failedRolledbackInstanceCount": 0
        }
      },
      "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
      "location": "westeurope"
    }
  ]
}
```

### <a name="azure-powershell"></a>Azure PowerShell
[Get-AzVmss](/powershell/module/az.compute/get-azvmss) コマンドレットを使用して、スケール セットの OS アップグレード履歴を確認します。 次の例は、*myResourceGroup* というリソース グループ内の *myVMSS* というスケール セットの OS アップグレード状態を確認する方法を説明したものです。

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
[az vmss get-os-upgrade-history](/cli/azure/vmss#az-vmss-get-os-upgrade-history) を使用して、スケール セットの OS アップグレード履歴を確認します。 Azure CLI 2.0.47 以上を使用してください。 次の例は、*myResourceGroup* というリソース グループ内の *myVMSS* というスケール セットの OS アップグレード状態を確認する方法を説明したものです。

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myVMSS
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>プラットフォーム OS イメージの最新バージョンを取得する方法

以下の例を使用して、OS の自動アップグレードがサポートされている SKU のイメージ バージョンを取得できます。

### <a name="rest-api"></a>REST API
```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2018-10-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
Get-AzVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
```azurecli-interactive
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="deploy-with-a-template"></a>テンプレートを使用したデプロイ

テンプレートを使用して、[Ubuntu 16.04-LTS](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json) などのサポート済みイメージの自動 OS アップグレードを使用したスケール セットをデプロイできます。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

## <a name="next-steps"></a>次の手順
スケール セットを使用して OS の自動アップグレードを使用する方法の例については、[GitHub リポジトリ](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade)を参照してください。
