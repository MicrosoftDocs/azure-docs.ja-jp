---
title: InfiniBand ドライバー拡張機能 - Azure Linux VM
description: Linux を実行しているの H および N シリーズのコンピューティング VM に InfiniBand ドライバーをインストールするための Microsoft Azure 拡張機能。
services: virtual-machines
documentationcenter: ''
author: vermagit
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: amverma
ms.openlocfilehash: 1219f5befb4de81850681a53c10f03dc81249d53
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559734"
---
# <a name="infiniband-driver-extension-for-linux"></a>Linux 用の InfiniBand ドライバー拡張機能

この拡張機能により、InfiniBand OFED ドライバーが、Linux を実行している InfiniBand と SR-IOV 対応 ('r' サイズ) の [H シリーズ](../sizes-hpc.md)と [N シリーズ](../sizes-gpu.md)の VM にインストールされます。 VM ファミリに応じて、Connect-X NIC に適したドライバーがインストールされます。

OFED ドライバーの手動インストール手順については、[こちら](../workloads/hpc/enable-infiniband.md#manual-installation)をご覧ください。

[Windows VM](hpc-compute-infiniband-windows.md) 用の InfiniBand ドライバーをインストールする拡張機能もあります。

## <a name="prerequisites"></a>前提条件

### <a name="operating-system"></a>オペレーティング システム

この拡張機能では、特定の OS バージョンのドライバー サポートに応じて、次の OS ディストリビューションをサポートしています。

| Distribution | Version |
|---|---|
| Linux: Ubuntu | 16.04 LTS、18.04 LTS、20.04 LTS |
| Linux: CentOS | 7.4、7.5、7.6、7.7、8.1、8.2 |
| Linux: Red Hat Enterprise Linux | 7.4、7.5、7.6、7.7、8.1、8.2 |

### <a name="internet-connectivity"></a>インターネット接続

InfiniBand ドライバー用の Microsoft Azure 拡張機能では、ターゲットの VM がインターネットに接続され、アクセスできるようになっている必要があります。

## <a name="extension-schema"></a>拡張機能のスキーマ

次の JSON は、拡張機能のスキーマを示しています。

```json
{
  "name": "<myExtensionName>",
  "type": "extensions",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <myVM>)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "InfiniBandDriverLinux",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Properties

| 名前 | 値/例 | データ型 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.HpcCompute | string |
| type | InfiniBandDriverLinux | string |
| typeHandlerVersion | 1.1 | INT |



## <a name="deployment"></a>デプロイ


### <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート 

Azure VM 拡張機能は、Azure Resource Manager テンプレートでデプロイできます。 テンプレートは、デプロイ後の構成が必要な仮想マシンを 1 つ以上デプロイするときに最適です。

仮想マシン拡張機能の JSON 構成は、仮想マシン リソース内に入れ子にすることも、Resource Manager JSON テンプレートのルートまたは最上位レベルに配置することもできます。 JSON 構成の配置は、リソースの名前と種類の値に影響します。 詳細については、[子リソースの名前と種類の設定](../../azure-resource-manager/templates/child-resource-name-type.md)に関する記事を参照してください。 

次の例では、拡張機能が仮想マシン リソース内で入れ子になっていることを前提としています。 拡張機能リソースを入れ子にすると、JSON は仮想マシンの `"resources": []` オブジェクトに配置されます。

```json
{
  "name": "myExtensionName",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', myVM)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "InfiniBandDriverLinux",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "InfiniBandDriverLinux" `
    -ExtensionType "InfiniBandDriverLinux" `
    -TypeHandlerVersion 1.1 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name InfiniBandDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.1 
```

### <a name="add-extension-to-a-virtual-machine-scale-set"></a>仮想マシン スケール セットに拡張機能を追加する

次の例では、*myResourceGroup* という名前のリソース グループにデプロイされた *myVMSS* という名前の既存の仮想マシン スケール セットのすべての RDMA 対応 VM に、最新のバージョン 1.1 の InfiniBandDriverLinux 拡張機能をインストールします。

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.1"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
```


## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshoot"></a>トラブルシューティング

拡張機能のデプロイ状態に関するデータは、Azure portal から取得することも、Azure PowerShell、Azure CLI を使用して取得することもできます。 特定の VM の拡張機能のデプロイ状態を確認するには、次のコマンドを実行します。

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

拡張機能の実行の出力は、次のファイルにログ記録されます。 インストールの状態を追跡する場合、および障害のトラブルシューティングを行うときは、このファイルを参照してください。

```bash
/var/log/azure/ib-vmext-status
```

### <a name="exit-codes"></a>終了コード

次の表では、拡張機能のインストール プロセスの終了コードに基づいて、その意味と推奨アクションを記載します。

| 終了コード | 意味 | 可能なアクション |
| :---: | --- | --- |
| 0 | 操作に成功しました |
| 1 | 拡張機能の使い方に誤りがあります | 実行の出力ログを確認します |
| 10 | Hyper-V と Azure 用の Linux Integration Services が使用できないかインストールされていません | lspci の出力を確認します |
| 11 | Mellanox InfiniBand は、この VM サイズでは見つかりません | [サポートされている VM サイズと OS](../sizes-hpc.md) を使用します |
| 12 | イメージの提供がサポートされていません |
| 13 | VM サイズがサポートされていません | InfiniBand 対応 ('r' サイズ) の [H シリーズ](../sizes-hpc.md)と [N シリーズ](../sizes-gpu.md)の VM を使用してデプロイします |
| 14 | 操作が失敗しました | 実行の出力ログを確認します |


### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/community/)で Azure エキスパートに問い合わせることができます。 または、[Azure サポート サイト](https://azure.microsoft.com/support/options/)からサポート インシデントを送信できます。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。

## <a name="next-steps"></a>次のステップ
InfiniBand 対応 ('r' サイズ) の詳細については、[H シリーズ](../sizes-hpc.md)と [N シリーズ](../sizes-gpu.md)の VM を参照してください。

> [!div class="nextstepaction"]
> [Linux VM の拡張機能と機能の詳細について学習する](features-linux.md)