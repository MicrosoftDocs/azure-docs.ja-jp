---
title: "Linux 用 Azure Network Watcher Agent 仮想マシン拡張機能 | Microsoft Docs"
description: "仮想マシン拡張機能を使用して、Linux 仮想マシンに Network Watcher Agent をデプロイします。"
services: virtual-machines-linux
documentationcenter: 
author: dennisg
manager: amku
editor: 
tags: azure-resource-manager
ms.assetid: 5c81e94c-e127-4dd2-ae83-a236c4512345
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
translationtype: Human Translation
ms.sourcegitcommit: 2de3e6d205dd126e1c3d9606ea1419669056d7f1
ms.openlocfilehash: eaadd531b9e05a54446e61f98584ae9d75470a5f
ms.lasthandoff: 02/22/2017


---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Linux 用 Network Watcher Agent 仮想マシン拡張機能

## <a name="overview"></a>概要

[Azure Network Watcher](https://review.docs.microsoft.com/en-us/azure/network-watcher/) は、Azure ネットワークの監視に使用できる、ネットワーク パフォーマンスの監視、診断、および分析サービスです。 Network Watcher Agent 仮想マシン拡張機能は、Azure 仮想マシンの一部の Network Watcher 機能に必要です。 これには、ネットワーク トラフィックのオンデマンドでのキャプチャなどの高度な機能が含まれます。

このドキュメントでは、Linux 用 Network Watcher Agent 仮想マシン拡張機能でサポートされているプラットフォームとデプロイ オプションについて詳しく説明します

## <a name="prerequisites"></a>前提条件

### <a name="operating-system"></a>オペレーティング システム

Network Watcher Agent 拡張機能は、次の Linux ディストリビューションに対して実行できます。

| ディストリビューション | バージョン |
|---|---|
| Ubuntu | 16.04 LTS、14.04 LTS、12.04 LTS |
| Debian | 7、8 |
| RedHat | 6.x、7.x |
| Oracle Linux | 7x |
| SUSE | 11、12 |
| openSUSE | 7.0 |
| CentOS | 7.0 |

現時点では、CoreOS はサポートされていません。

### <a name="internet-connectivity"></a>インターネット接続

一部の Network Watcher Agent 機能では、ターゲット仮想マシンがインターネットに接続されている必要があります。 送信接続を確立できない場合、一部の Network Watcher Agent 機能が正しく動作しなかったり、使用できなくなったりすることがあります。 詳細については、[Network Watcher のドキュメント](https://review.docs.microsoft.com/en-us/azure/network-watcher/)をご覧ください。

## <a name="extension-schema"></a>拡張機能のスキーマ

次の JSON は、Network Watcher Agent 拡張機能のスキーマを示しています。 現時点では、この拡張機能はユーザーが指定した設定を必要とせず、サポートもしていません。既定の構成が使用されます。

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
        "type": "NetworkWatcherAgentLinux",
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
| type | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>テンプレートのデプロイ

Azure VM 拡張機能は、Azure Resource Manager テンプレートでデプロイできます。 前のセクションで詳しく説明した JSON スキーマを Azure Resource Manager テンプレートで使用すると、Azure Resource Manager テンプレートのデプロイ時に Network Watcher Agent 拡張機能を実行できます。

## <a name="azure-cli-deployment"></a>Azure CLI でのデプロイ

Azure CLI を使用して、Network Watcher Agent VM 拡張機能を既存の仮想マシンにデプロイできます。

```azurecli
azure vm extension set myResourceGroup1 myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="troubleshooting-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshooting"></a>トラブルシューティング

拡張機能のデプロイ状態に関するデータを取得するには、Azure Portal か Azure CLI を使用します。 特定の VM の拡張機能のデプロイ状態を確認するには、Azure CLI を使用して次のコマンドを実行します。

```azurecli
azure vm extension get myResourceGroup1 myVM1
```

拡張機能の実行の出力は、次のディレクトリ内のファイルにログ記録されます。

`
/var/log/azure/Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentLinux/
`

### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、Network Watcher のドキュメントをご覧ください。また、[MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/en-us/support/forums/)で Azure エキスパートに問い合わせることもできます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/en-us/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/en-us/support/faq/)」を参照してください。

