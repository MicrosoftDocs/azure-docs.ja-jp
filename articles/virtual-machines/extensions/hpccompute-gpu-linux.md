---
title: NVIDIA GPU ドライバー拡張機能 - Azure Linux VM
description: Linux を実行中の N シリーズのコンピューティング VM に NVIDIA GPU ドライバーをインストールする Microsoft Azure 拡張機能です。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: amverma
ms.openlocfilehash: fa2b82f3246fd87830010f572ba23aa2df075053
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102566228"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>Linux 用の NVIDIA GPU ドライバー拡張機能

## <a name="overview"></a>概要

この拡張機能は、Linux N シリーズ VM に NVIDIA GPU ドライバーをインストールします。 VM ファミリに応じて、この拡張機能では CUDA ドライバーまたは GRID ドライバーがインストールされます。 この拡張機能を使用して NVIDIA ドライバーをインストールする際は、[NVIDIA のエンドユーザー使用許諾契約書](https://go.microsoft.com/fwlink/?linkid=874330)の条項を受け入れ、同意します。 インストール プロセス中に、ドライバーのセットアップを完了するために仮想マシンが再起動することがあります。

ドライバーの手動インストールの手順と現在サポートされているバージョンについては、[こちら](../linux/n-series-driver-setup.md)をご覧ください。
NVIDIA GPU ドライバーを [Windows の N シリーズ VM](hpccompute-gpu-windows.md) にインストールする拡張機能も利用可能です。

## <a name="prerequisites"></a>前提条件

### <a name="operating-system"></a>オペレーティング システム

この拡張機能では、特定の OS バージョンのドライバー サポートに応じて、次の OS ディストリビューションをサポートしています。

| Distribution | Version |
|---|---|
| Linux: Ubuntu | 16.04 LTS、18.04 LTS |
| Linux: Red Hat Enterprise Linux | 7.3、7.4、7.5、7.6、7.7、7.8 |
| Linux: CentOS | 7.3、7.4、7.5、7.6、7.7、7.8 |

### <a name="internet-connectivity"></a>インターネット接続

NVIDIA GPU ドライバー用の Microsoft Azure 拡張機能では、ターゲットの仮想マシンがインターネットに接続され、アクセスできるようになっている必要があります。

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
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.3",
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
| type | NvidiaGpuDriverLinux | string |
| typeHandlerVersion | 1.3 | INT |

### <a name="settings"></a>設定

すべての設定は省略可能です。 既定の動作では、ドライバーのインストールに必要ない場合はカーネルを更新せず、サポートされている最新のドライバーと (必要に応じて) CUDA Toolkit をインストールします。

| 名前 | 説明 | Default value | 有効な値 | データ型 |
| ---- | ---- | ---- | ---- | ---- |
| updateOS | ドライバーのインストールに必要ない場合でも、カーネルを更新します。 | false | true、false | boolean |
| driverVersion | NV: GRID ドライバーのバージョン<br> NC/ND: CUDA Toolkit のバージョン。 選択した CUDA の最新のドライバーが自動的にインストールされます。 | latest | サポートされているドライバーのバージョンの[リスト](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json) | string |
| installCUDA | CUDA Toolkit をインストールします。 NC/ND シリーズの VM のみに関係します。 | true | true、false | boolean |


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
    "type": "NvidiaGpuDriverLinux",
    "typeHandlerVersion": "1.3",
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
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.3 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

次の例では、上記の Azure Resource Manager および PowerShell の例をミラー化します。

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.3 
```

また、次の例では、既定以外のドライバーのインストールの例として、2 つのオプションのカスタム設定も追加します。 具体的には、OS カーネルを最新に更新し、特定の CUDA Toolkit バージョンのドライバーをインストールします。 ここでも、"--settings" は省略可能であり、既定値であることに注意してください。 カーネルを更新すると、拡張機能のインストール時間が長くなる場合があることに注意してください。 また、CUDA Toolkit の特定の (古い) バージョンの選択は、新しいカーネルと互換性がない場合があります。

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.3 \
  --settings '{ \
    "updateOS": true, \
    "driverVersion": "10.0.130" \
  }'
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

拡張機能の実行の出力は、次のファイルにログ記録されます。 (実行時間の長い) インストールの状態を追跡する場合、および障害のトラブルシューティングを行うときは、このファイルを参照します。

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>終了コード

| 終了コード | 意味 | 可能なアクション |
| :---: | --- | --- |
| 0 | 操作に成功しました |
| 1 | 拡張機能の使い方に誤りがあります | 実行の出力ログを確認します |
| 10 | Hyper-V と Azure 用の Linux Integration Services が使用できないかインストールされていません | lspci の出力を確認します |
| 11 | この VM サイズで NVIDIA GPU が見つかりません | [サポートされている VM サイズと OS](../linux/n-series-driver-setup.md) を使用します |
| 12 | イメージの提供がサポートされていません |
| 13 | VM サイズがサポートされていません | N シリーズ VM を使用してデプロイします |
| 14 | 操作が失敗しました | 実行の出力ログを確認します |


### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/community/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。

## <a name="next-steps"></a>次のステップ
拡張機能の詳細については、「[Linux 用の仮想マシンの拡張機能とその機能](features-linux.md)」を参照してください。

N シリーズ VM の詳細については、「[GPU 最適化済み仮想マシンのサイズ](../sizes-gpu.md)」を参照してください。
