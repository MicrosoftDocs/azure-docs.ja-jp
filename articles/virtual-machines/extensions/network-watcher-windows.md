---
title: Windows 用 Azure Network Watcher Agent 仮想マシン拡張機能
description: 仮想マシン拡張機能を使用して、Windows 仮想マシンに Network Watcher Agent をデプロイします。
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 02/14/2017
ms.openlocfilehash: d336a39714712e5436086e22ad24fc942a7d850a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563542"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Windows 用 Network Watcher Agent 仮想マシン拡張機能

## <a name="overview"></a>概要

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) は、Azure ネットワークの監視に使用できる、ネットワーク パフォーマンスの監視、診断、および分析サービスです。 Network Watcher Agent 仮想マシン拡張機能は、オンデマンドでネットワーク トラフィックをキャプチャするためと、Azure 仮想マシンに関するその他の高度な機能を使用するための要件です。


このドキュメントでは、Windows 用 Network Watcher Agent 仮想マシン拡張機能でサポートされているプラットフォームとデプロイ オプションについて詳しく説明します。 エージェントのインストールによって、仮想マシンが中断されることも、再起動が必要になることもありません。 デプロイする仮想マシンに拡張機能をデプロイすることができます。 仮想マシンが Azure サービスによってデプロイされる場合は、サービスのドキュメントを調べて、仮想マシンへの拡張機能のインストールが許可されるかどうかを確認します。

## <a name="prerequisites"></a>前提条件

### <a name="operating-system"></a>オペレーティング システム

Windows 用の Network Watcher Agent 拡張機能は、Windows Server 2008 R2、2012、2012 R2、2016、2019 の各リリースで実行できます。 現時点では、Nano Server はサポートされていません。

### <a name="internet-connectivity"></a>インターネット接続

一部の Network Watcher Agent 機能では、ターゲット仮想マシンがインターネットに接続されている必要があります。 発信接続を確立できなければ、Network Watcher Agent はパケット キャプチャをストレージ アカウントにアップロードできません。 詳細については、[Network Watcher のドキュメント](../../network-watcher/network-watcher-monitoring-overview.md)をご覧ください。

## <a name="extension-schema"></a>拡張機能のスキーマ

次の JSON は、Network Watcher Agent 拡張機能のスキーマを示しています。 この拡張機能はユーザーが指定した設定を必要とせず、サポートもしていません。既定の構成が使用されます。

```json
{
    "type": "extensions",
    "name": "Microsoft.Azure.NetworkWatcher",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.NetworkWatcher",
        "type": "NetworkWatcherAgentWindows",
        "typeHandlerVersion": "1.4",
        "autoUpgradeMinorVersion": true
    }
}
```

### <a name="property-values"></a>プロパティ値

| 名前 | 値/例 |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.Azure.NetworkWatcher |
| type | NetworkWatcherAgentWindows |
| typeHandlerVersion | 1.4 |


## <a name="template-deployment"></a>テンプレートのデプロイ

Azure VM 拡張機能は、Azure Resource Manager テンプレートでデプロイできます。 前のセクションで詳しく説明した JSON スキーマを Azure Resource Manager テンプレートで使用して、Azure Resource Manager テンプレートのデプロイ時に Network Watcher Agent 拡張機能を実行できます。

## <a name="powershell-deployment"></a>PowerShell でのデプロイ

`Set-AzVMExtension` コマンドを使用して、Network Watcher Agent 仮想マシン拡張機能を既存の仮想マシンにデプロイします。

```powershell
Set-AzVMExtension `
  -ResourceGroupName "myResourceGroup1" `
  -Location "WestUS" `
  -VMName "myVM1" `
  -Name "networkWatcherAgent" `
  -Publisher "Microsoft.Azure.NetworkWatcher" `
  -Type "NetworkWatcherAgentWindows" `
  -TypeHandlerVersion "1.4"
```

## <a name="troubleshooting-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshooting"></a>トラブルシューティング

Azure Portal と PowerShell を使用して、拡張機能のデプロイ状態に関するデータを取得できます。 特定の VM での拡張機能のデプロイ状態を確認するには、Azure PowerShell モジュールを使用して次のコマンドを実行します。

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

拡張機能の実行の出力は、次のディレクトリ内のファイルにログ記録されます。

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、Network Watcher ユーザー ガイドをご覧ください。また、[MSDN の Azure フォーラムとスタック オーバーフロー フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることもできます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。
