---
title: Linux 用 Azure Network Watcher Agent 仮想マシン拡張機能
description: 仮想マシン拡張機能を使用して、Linux 仮想マシンに Network Watcher Agent をデプロイします。
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 02/14/2017
ms.openlocfilehash: bc252e560df782625d795b30c6688a34f5c2bd79
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563593"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Linux 用 Network Watcher Agent 仮想マシン拡張機能

## <a name="overview"></a>概要

[Azure Network Watcher](../../network-watcher/index.yml) は、Azure ネットワークの監視に使用できる、ネットワーク パフォーマンスの監視、診断、および分析サービスです。 Network Watcher Agent 仮想マシン (VM) 拡張機能は、オンデマンドでのネットワーク トラフィックの捕捉やその他の高度な機能などの Azure VM の一部 Network Watcher 機能に必須の機能です。

この記事では、Linux 用 Network Watcher Agent 仮想マシン拡張機能でサポートされているプラットフォームとデプロイ オプションについて詳しく説明します エージェントのインストールによって、仮想マシンが中断されることも、再起動が必要になることもありません。 デプロイする仮想マシンに拡張機能をデプロイすることができます。 仮想マシンが Azure サービスによってデプロイされる場合は、サービスのドキュメントを調べて、仮想マシンへの拡張機能のインストールが許可されるかどうかを確認します。

## <a name="prerequisites"></a>前提条件

### <a name="operating-system"></a>オペレーティング システム

Network Watcher Agent 拡張機能は、次の Linux ディストリビューションで構成できます。

| Distribution | Version |
|---|---|
| Ubuntu | 12 以降 |
| Debian | 7 および 8 |
| Red Hat | 6、7 |
| Oracle Linux | 6.8 以降、7 |
| SUSE Linux Enterprise Server | 11、12 |
| OpenSUSE Leap | 42.3 以降 |
| CentOS | 6.5 以降、7 |
| CoreOS | 899.17.0 以降 |


### <a name="internet-connectivity"></a>インターネット接続

一部の Network Watcher Agent 機能では、仮想マシンがインターネットに接続されている必要があります。 送信接続を確立できない場合、一部の Network Watcher Agent 機能が正しく動作しなかったり、使用できなくなったりすることがあります。 このエージェントを必要とする Network Watcher 機能についての詳細は、[Network Watcher ドキュメント](../../network-watcher/index.yml) をご覧ください。

## <a name="extension-schema"></a>拡張機能のスキーマ

次の JSON は、Network Watcher Agent 拡張機能のスキーマを示しています。 この拡張機能は、設定のユーザー指定を必要とせず、サポートもしていません。 既定の構成のみ利用できます。

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

Azure VM 拡張機能は、Azure Resource Manager テンプレートを使ってデプロイできます。 Network Watcher Agent 拡張機能をデプロイするには、テンプレートで前回の JSON スキーマを利用します。

## <a name="azure-classic-cli-deployment"></a>Azure クラシック CLI でのデプロイ

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

次の例では、従来のデプロイ モデルを使用してデプロイされた既存の VM に Network Watcher Agent VM 拡張機能をデプロイします。

```console
azure config mode asm
azure vm extension set myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="azure-cli-deployment"></a>Azure CLI でのデプロイ

次の例では、Resource Manager を使用してデプロイされた既存の VM に Network Watcher Agent VM 拡張機能をデプロイします。

```azurecli
az vm extension set --resource-group myResourceGroup1 --vm-name myVM1 --name NetworkWatcherAgentLinux --publisher Microsoft.Azure.NetworkWatcher --version 1.4
```

## <a name="troubleshooting-and-support"></a>トラブルシューティングとサポート

### <a name="troubleshooting"></a>トラブルシューティング

Azure Portal または Azure CLI を使用して、拡張機能のデプロイ状態に関するデータを取得できます。

次の例は、Azure CLI で Resource Manager を使用してデプロイされた VM の NetworkWatcherAgentLinux に対する拡張機能のデプロイ状態を示しています。

```azurecli
az vm extension show --name NetworkWatcherAgentLinux --resource-group myResourceGroup1 --vm-name myVM1
```

### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、[Network Watcher のドキュメント](../../network-watcher/index.yml)をご覧ください。また、[MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることもできます。 または、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。 Azure サポートのご利用方法についての詳細は、「[Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。
