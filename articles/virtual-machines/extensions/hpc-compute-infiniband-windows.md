---
title: InfiniBand ドライバー拡張機能 - Azure Windows VM
description: Windows を実行している H および N シリーズのコンピューティング VM に InfiniBand ドライバーをインストールするための Microsoft Azure 拡張機能です。
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: amverma
ms.openlocfilehash: ccc9df8078bb7fec8be7d72b0ae18ed416bb10ca
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87094629"
---
# <a name="infiniband-driver-extension-for-windows"></a>Windows 用の InfiniBand ドライバー拡張機能

この拡張機能により、InfiniBand ND ドライバー (SR-IOV 以外が有効な場合) および OFED ドライバー (SR-IOV が有効な場合) が、Windows を実行している InfiniBand と SR-IOV 対応 ('r' サイズ) の [H シリーズ](../sizes-hpc.md)と [N シリーズ](../sizes-gpu.md)の VM にインストールされます。 VM ファミリに応じて、Connect-X NIC に適したドライバーがインストールされます。

[Linux VM](hpc-compute-infiniband-linux.md) 用の InfiniBand ドライバーをインストールする拡張機能もあります。

## <a name="prerequisites"></a>前提条件

### <a name="operating-system"></a>オペレーティング システム

この拡張機能では、特定の OS バージョンのドライバー サポートに応じて、次の OS ディストリビューションをサポートしています。

| Distribution | Version |
|---|---|
| Windows 10 | コア |
| Windows Server 2019 | コア |
| Windows Server 2016 | コア |
| Windows Server 2012 R2 | コア |
| Windows Server 2012 | コア |

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
    "type": "InfiniBandDriverWindows",
    "typeHandlerVersion": "1.2",
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
| type | InfiniBandDriverWindows | string |
| typeHandlerVersion | 1.2 | INT |



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
    "type": "InfiniBandDriverWindows",
    "typeHandlerVersion": "1.2",
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
    -ExtensionName "InfiniBandDriverWindows" `
    -ExtensionType "InfiniBandDriverWindows" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name InfiniBandDriverWindows \
  --publisher Microsoft.HpcCompute \
  --version 1.2 
```

### <a name="add-extension-to-a-virtual-machine-scale-set"></a>仮想マシン スケール セットに拡張機能を追加する

次の例では、*myResourceGroup* という名前のリソース グループにデプロイされた *myVMSS* という名前の既存の仮想マシン スケール セットのすべての RDMA 対応 VM に、最新バージョン 1.2 の InfiniBandDriverWindows 拡張機能をインストールします。

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.2"
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

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.InfiniBandDriverWindows\
```

### <a name="exit-codes"></a>終了コード

次の表では、拡張機能のインストール プロセスの終了コードに基づいて、その意味と推奨アクションを記載します。

| エラー コード | 意味 | 可能なアクション |
| :---: | --- | --- |
| 0 | 操作に成功しました |
| 3010 | 操作に成功しました。 再起動が必要です。 |
| 100 | 操作はサポートされていないか、完了できませんでした。 | 考えられる原因:PowerShell のバージョンがサポートされていない、VM のサイズが InfiniBand 対応の VM ではない、またはデータのダウンロードに失敗しています。 ログ ファイルをチェックして、エラーの原因を特定します。 |
| 240、840 | 操作がタイムアウトしました。 | 操作を再試行します。 |
| -1 | 例外が発生しました。 | ログ ファイルを確認して例外の原因を特定します。 |

### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/community/)で Azure エキスパートに問い合わせることができます。 または、[Azure サポート サイト](https://azure.microsoft.com/support/options/)からサポート インシデントを送信できます。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。

## <a name="next-steps"></a>次のステップ
InfiniBand 対応 ('r' サイズ) の詳細については、[H シリーズ](../sizes-hpc.md)と [N シリーズ](../sizes-gpu.md)の VM を参照してください。

> [!div class="nextstepaction"]
> [Linux VM の拡張機能と機能の詳細について学習する](features-linux.md)