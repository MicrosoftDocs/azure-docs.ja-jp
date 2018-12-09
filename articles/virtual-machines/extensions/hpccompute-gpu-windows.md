---
title: NVIDIA GPU ドライバー拡張機能 - Azure Windows VM | Microsoft Docs
description: Windows を実行中の N シリーズのコンピューティング VM に NVIDIA GPU ドライバーをインストールするための Microsoft Azure 拡張機能です。
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/15/2018
ms.author: roiyz
ms.openlocfilehash: ee74d4520e867604f50c70f2b6449f12ff3bd8b9
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/28/2018
ms.locfileid: "52495965"
---
# <a name="nvidia-gpu-driver-extension-for-windows"></a>Windows 用の NVIDIA GPU ドライバー拡張機能

## <a name="overview"></a>概要

この拡張機能は、Windows の N シリーズ VM に NVIDIA GPU ドライバーをインストールします。 VM ファミリに応じて、この拡張機能では CUDA ドライバーまたは GRID ドライバーがインストールされます。 この拡張機能を使用して NVIDIA ドライバーをインストールする際は、[NVIDIA のエンドユーザー使用許諾契約書](https://go.microsoft.com/fwlink/?linkid=874330)の条項を受け入れ、同意します。 インストール プロセス中に、ドライバーのセットアップを完了するために仮想マシンが再起動することがあります。

NVIDIA GPU ドライバーを [Linux の N シリーズ VM](hpccompute-gpu-linux.md) にインストールするための拡張機能も利用可能です。

## <a name="prerequisites"></a>前提条件

### <a name="operating-system"></a>オペレーティング システム

この拡張機能は､次の OS に対応しています｡

| ディストリビューション | Version |
|---|---|
| Windows 10 | コア |
| Windows Server 2016 | コア |
| Windows Server 2012R2 | コア |

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
    "type": "NvidiaGpuDriverWindows",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Properties

| Name | 値/例 | データ型 |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.HpcCompute | string |
| type | NvidiaGpuDriverWindows | string |
| typeHandlerVersion | 1.2 | int |

### <a name="settings"></a>設定

すべての設定は省略可能です。 既定の動作では、サポートされている最新のドライバーが必要に応じてインストールされます。

| Name | 説明 | 既定値 | 有効な値 | データ型 |
| ---- | ---- | ---- | ---- | ---- |
| driverVersion | NV: GRID ドライバーのバージョン<br> NC/ND: CUDA ドライバーのバージョン | latest | GRID: "411.81"、"391.81"、"391.58"、"391.03"<br> CUDA: "398.75"、"397.44"、"390.85" | string |
| installGridND | ND シリーズ VM への GRID ドライバーのインストール | false | true、false | ブール値 |

## <a name="deployment"></a>Deployment


### <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート 

Azure VM 拡張機能は、Azure Resource Manager テンプレートでデプロイできます。 テンプレートは、デプロイ後の構成が必要な仮想マシンを 1 つ以上デプロイするときに最適です。

仮想マシン拡張機能の JSON 構成は、仮想マシン リソース内に入れ子にすることも、Resource Manager JSON テンプレートのルートまたは最上位レベルに配置することもできます。 JSON 構成の配置は、リソースの名前と種類の値に影響します。 詳細については、[子リソースの名前と種類の設定](../../azure-resource-manager/resource-manager-template-child-resource.md)に関する記事を参照してください。 

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
    "type": "NvidiaGpuDriverWindows",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzureRmVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "NvidiaGpuDriverWindows" `
    -ExtensionType "NvidiaGpuDriverWindows" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

次の例では、上記の ARM と PowerShell の例を反映し、既定以外のドライバーのインストールの例としてカスタム設定も追加します。 具体的には、ND シリーズ VM がプロビジョニングされる場合でも、特定の GRID ドライバーがインストールされます。

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name NvidiaGpuDriverWindows `
  --publisher Microsoft.HpcCompute `
  --version 1.2 `
  --settings '{ `
    "driverVersion": "391.03",
    "installGridND": true
  }'
```

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshoot"></a>トラブルシューティング

拡張機能のデプロイ状態に関するデータは、Azure portal から取得することも、Azure PowerShell、Azure CLI を使用して取得することもできます。 特定の VM の拡張機能のデプロイ状態を確認するには、次のコマンドを実行します。

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

拡張機能の実行の出力は、次のディレクトリにログ記録されます。

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverMicrosoft\
```

### <a name="error-codes"></a>エラー コード

| エラー コード | 意味 | 可能なアクション |
| :---: | --- | --- |
| 0 | 操作に成功しました |
| 1 | 操作に成功しました。 再起動が必要です。 |
| 100 | 操作はサポートされていないか、完了できませんでした。 | 考えられる原因: PowerShell のバージョンがサポートされていない、VM のサイズが N シリーズの VM ではない、またはデータのダウンロードに失敗しています。 ログ ファイルをチェックして、エラーの原因を特定します。 |
| 240、840 | 操作がタイムアウトしました。 | 操作を再試行します。 |
| -1 | 例外が発生しました。 | ログ ファイルを確認して例外の原因を特定します。 |
| -5x | 保留中の再起動により操作が中断されました。 | VM を再起動します。 インストールは再起動後に続行されます。 アンインストールは手動で行う必要があります。 |


### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/community/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。

## <a name="next-steps"></a>次の手順
拡張機能の詳細については、「[Windows 用の仮想マシン拡張機能とその機能](features-windows.md)」を参照してください。

N シリーズ VM の詳細については、「[GPU 最適化済み仮想マシンのサイズ](../windows/sizes-gpu.md)」を参照してください。
