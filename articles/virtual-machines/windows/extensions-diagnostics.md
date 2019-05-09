---
title: Windows の Azure Diagnostics 拡張機能 | Microsoft Docs
description: Azure Diagnostics 拡張機能を使用して Azure Windows VM を監視する
services: virtual-machines-windows
documentationcenter: ''
author: johnkemnetz
manager: ashwink
editor: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/06/2018
ms.author: johnkem
ms.openlocfilehash: 772c0a4b6cf57aa428f722fb90a0673f9e703627
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2019
ms.locfileid: "55982796"
---
# <a name="azure-diagnostics-extension-for-windows-vms"></a>Windows VM 用の Azure Diagnostics 拡張機能

## <a name="overview"></a>概要

Azure Diagnostics VM 拡張機能を使用すると、Windows VM からパフォーマンス カウンターやイベント ログなどの監視データを収集できます。 Azure Storage アカウントや Azure イベント ハブなど、収集するデータとデータの保存先を細かく指定できます。 Azure Portal でこのデータを使用してグラフを作成したり、メトリック アラートを作成したりすることもできます。

## <a name="prerequisites"></a>前提条件

### <a name="operating-system"></a>オペレーティング システム

Azure Diagnostics 拡張機能は、Windows 10 クライアント、Windows Server 2008 R2、2012、2012 R2、2016 で実行できます。

### <a name="internet-connectivity"></a>インターネット接続

Azure Diagnostics 拡張機能では、ターゲットの仮想マシンがインターネットに接続されている必要があります。 

## <a name="extension-schema"></a>拡張機能のスキーマ

[このドキュメントでは、Azure 診断拡張機能のスキーマとプロパティ値について説明します。](../../azure-monitor/platform/diagnostics-extension-schema-1dot3.md)

## <a name="template-deployment"></a>テンプレートのデプロイ

Azure VM 拡張機能は、Azure Resource Manager テンプレートでデプロイできます。 前のセクションで詳しく説明した JSON スキーマを Azure Resource Manager テンプレートで使うと、Azure Resource Manager テンプレートのデプロイ時に Azure Diagnostics 拡張機能を実行できます。 「[Windows VM と Azure Resource Manager テンプレートで監視と診断を利用する](extensions-diagnostics-template.md)」を参照してください。

## <a name="azure-cli-deployment"></a>Azure CLI でのデプロイ

Azure CLI を使用して、Azure Diagnostics 拡張機能を既存の仮想マシンにデプロイすることができます。 保護された設定と設定のプロパティを上記の拡張機能スキーマの有効な JSON に置き換えます。 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --version 1.9.0.0 --protected-settings protected-settings.json \
  --settings public-settings.json 
```

## <a name="powershell-deployment"></a>PowerShell でのデプロイ

`Set-AzVMDiagnosticsExtension` コマンドを使用して、Azure 診断拡張機能を既存の仮想マシンに追加することができます。 また、「[PowerShell を使用して Windows を実行している仮想マシンで Azure Diagnostics を有効にする](ps-extensions-diagnostics.md)」も参照してください。

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]


```powershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup `
  -VMName $vm_name `
  -DiagnosticsConfigurationPath $diagnosticsconfig_path
```

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshoot"></a>トラブルシューティング

拡張機能のデプロイ状態に関するデータを取得するには、Azure Portal か Azure CLI を使用します。 特定の VM の拡張機能のデプロイ状態を確認するには、Azure CLI を使用して次のコマンドを実行します。

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Azure 診断拡張機能の包括的なトラブルシューティング ガイドについては、[こちらの記事](../../azure-monitor/platform/diagnostics-extension-troubleshooting.md)を参照してください。

### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。

## <a name="next-steps"></a>次の手順
* [Azure 診断拡張機能の詳細](../../azure-monitor/platform/diagnostics-extension-overview.md)
* [拡張機能スキーマとバージョンの確認](../../azure-monitor/platform/diagnostics-extension-schema.md)
