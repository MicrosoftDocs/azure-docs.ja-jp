---
title: AMD GPU ドライバー拡張機能 - Azure Windows VM
description: Windows を実行中の NVv4 シリーズの VM に AMD GPU ドライバーをインストールするための Microsoft Azure 拡張機能です。
services: virtual-machines-windows
author: vikancha-MSFT
manager: jkabat
ms.topic: article
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: windows
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/10/2020
ms.author: vikancha
ms.openlocfilehash: 42dac9edc91d7cb935e8c20398c4d31343b358e1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559683"
---
# <a name="amd-gpu-driver-extension-for-windows"></a>Windows 用の AMD GPU ドライバー拡張機能

この記事では、Windows [NVv4 シリーズ](../nvv4-series.md)の VM に AMD GPU ドライバーを配置するための VM 拡張機能の概要について説明します。 この拡張機能を使用して AMD ドライバーをインストールすると、[AMD のエンドユーザー使用許諾契約書](https://amd.com/radeonsoftwarems)の条項を受け入れ、同意することになります。 インストール プロセス中に、ドライバーのセットアップを完了するために仮想マシンが再起動することがあります。

ドライバーの手動インストールの手順と現在サポートされているバージョンについては、[こちら](../windows/n-series-amd-driver-setup.md)をご覧ください。

## <a name="prerequisites"></a>前提条件

### <a name="operating-system"></a>オペレーティング システム

この拡張機能は､次の OS に対応しています｡

| Distribution | Version |
|---|---|
| Windows 10 EMS | ビルド 1903 |
| Windows 10 | ビルド 1809 |
| Windows Server 2016 | コア |
| Windows Server 2019 | コア |

### <a name="internet-connectivity"></a>インターネット接続

AMD GPU ドライバー用の Microsoft Azure 拡張機能では、ターゲットの VM がインターネットに接続され、アクセスできるようになっている必要があります。

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
    "type": "AmdGpuDriverWindows",
    "typeHandlerVersion": "1.0",
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
| type | AmdGpuDriverWindows | string |
| typeHandlerVersion | 1.0 | INT |


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
    "type": "AmdGpuDriverWindows",
    "typeHandlerVersion": "1.0",
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
    -ExtensionName "AmdGpuDriverWindows" `
    -ExtensionType "AmdGpuDriverWindows" `
    -TypeHandlerVersion 1.0 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name AmdGpuDriverWindows `
  --publisher Microsoft.HpcCompute `
  --version 1.0 `
  --settings '{ `
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

拡張機能の実行の出力は、次のディレクトリにログ記録されます。

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.AmdGpuDriverMicrosoft\
```

### <a name="error-codes"></a>エラー コード

| エラー コード | 意味 | 可能なアクション |
| :---: | --- | --- |
| 0 | 操作に成功しました |
| 1 | 操作に成功しました。 再起動が必要です。 |
| 100 | 操作はサポートされていないか、完了できませんでした。 | 考えられる原因:PowerShell のバージョンがサポートされていない、VM のサイズが N シリーズの VM ではない、またはデータのダウンロードに失敗しています。 ログ ファイルをチェックして、エラーの原因を特定します。 |
| 240、840 | 操作がタイムアウトしました。 | 操作を再試行します。 |
| -1 | 例外が発生しました。 | ログ ファイルを確認して例外の原因を特定します。 |
| -5x | 保留中の再起動により操作が中断されました。 | VM を再起動します。 インストールは再起動後に続行されます。 アンインストールは手動で行う必要があります。 |


### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/community/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。

## <a name="next-steps"></a>次のステップ
拡張機能の詳細については、「[Windows 用の仮想マシン拡張機能とその機能](features-windows.md)」を参照してください。

N シリーズ VM の詳細については、「[GPU 最適化済み仮想マシンのサイズ](../sizes-gpu.md)」を参照してください。
