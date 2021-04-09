---
title: Azure Network Watcher でパケット キャプチャを管理する - Azure CLI | Microsoft Docs
description: このページでは、Azure CLI を使って Network Watcher のパケット キャプチャ機能を管理する方法を説明します
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: efb8fba655570a661b6b001eb93942475eb2170d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101698639"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-azure-cli"></a>Azure CLI を使用して Azure Network Watcher でパケット キャプチャを管理する

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

Network Watcher のパケット キャプチャを使用すると、仮想マシンとの間で送受信されるトラフィックを追跡するキャプチャ セッションを作成できます。 必要なトラフィックのみを確実にキャプチャするためにキャプチャ セッション用のフィルターが用意されています。 パケット キャプチャは、事後と事前に、ネットワークの異常を診断するのに役立ちます。 その他の用途には、ネットワーク統計の収集、ネットワークへの侵入に関する情報を取得などがあり、クライアント サーバー間の通信のデバッグなどに役立ちます。 パケット キャプチャをリモートでトリガーすることができることで、この機能によってパケット キャプチャを手動で、目的のマシン上で実行する負荷が軽減されて、貴重な時間の節約になります。

この記事の手順を実行するには、[Mac、Linux、Windows 用の Azure コマンドライン インターフェイス (Azure CLI) をインストール](/cli/azure/install-azure-cli)する必要があります。

この記事では、パケット キャプチャで現在利用できるさまざまな管理作業について説明します。

- [**パケット キャプチャを開始する**](#start-a-packet-capture)
- [**パケット キャプチャを停止する**](#stop-a-packet-capture)
- [**パケット キャプチャを削除する**](#delete-a-packet-capture)
- [**パケット キャプチャをダウンロードする**](#download-a-packet-capture)

## <a name="before-you-begin"></a>開始する前に

この記事では、次のリソースがあることを前提としています。

- パケット キャプチャを作成するリージョン内の Network Watcher インスタンス
- パケット キャプチャ拡張機能が有効になっている仮想マシン

> [!IMPORTANT]
> パケット キャプチャを仮想マシン上で実行するにはエージェントが必要です。 エージェントは、拡張機能としてインストールされます。 VM 拡張機能については、[仮想マシン拡張機能とその機能](../virtual-machines/extensions/features-windows.md)に関するページを参照してください。

## <a name="install-vm-extension"></a>VM 拡張機能をインストールする

### <a name="step-1"></a>手順 1

ゲスト仮想マシンにパケット キャプチャ エージェントをインストールするには、`az vm extension set` コマンドを実行します。

Windows 仮想マシンの場合:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentWindows --version 1.4
```

Linux 仮想マシンの場合:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentLinux --version 1.4
```

### <a name="step-2"></a>手順 2.

エージェントがインストールされていることを確認するために、リソース グループと仮想マシン名を渡して `vm extension show` コマンドレットを実行します。 結果のリストを調べて、エージェントがインストールされていることを確認します。

Windows 仮想マシンの場合:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name NetworkWatcherAgentWindows
```

Linux 仮想マシンの場合:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name AzureNetworkWatcherExtension
```

以下のサンプルは、`az vm extension show` の実行に対する応答の例です。

```json
{
  "autoUpgradeMinorVersion": true,
  "forceUpdateTag": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/extensions/NetworkWatcherAgentWindows",
  "instanceView": null,
  "location": "westcentralus",
  "name": "NetworkWatcherAgentWindows",
  "protectedSettings": null,
  "provisioningState": "Succeeded",
  "publisher": "Microsoft.Azure.NetworkWatcher",
  "resourceGroup": "{resourceGroupName}",
  "settings": null,
  "tags": null,
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "typeHandlerVersion": "1.4",
  "virtualMachineExtensionType": "NetworkWatcherAgentWindows"
}
```

## <a name="start-a-packet-capture"></a>パケット キャプチャを開始する

上記の手順が完了すると、パケット キャプチャ エージェントは仮想マシンにインストールされます。

### <a name="step-1"></a>手順 1

ストレージ アカウントを取得します。 このストレージ アカウントは、パケット キャプチャ ファイルの格納に使用されます。

```azurecli-interactive
az storage account list
```

### <a name="step-2"></a>手順 2.

この時点で、パケット キャプチャを作成する準備が整いました。この時点で、パケット キャプチャを作成する準備が整いました。  まず、構成するパラメーターを確認してみましょう。 フィルターは、パケットキャプチャによって格納されるデータを制限するために使用できるパラメーターです。 次の例では、いくつかのフィルターを使用してパケット キャプチャを設定します。  最初の 3 つのフィルターは、ローカル IP 10.0.0.3 から宛先ポート 20、80、443 への発信 TCP トラフィックのみを収集します。  最後のフィルターは、UDP トラフィックのみを収集します。

```azurecli-interactive
az network watcher packet-capture create --resource-group {resourceGroupName} --vm {vmName} --name packetCaptureName --storage-account {storageAccountName} --filters "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

次の例は、`az network watcher packet-capture create` コマンドを実行したときに予想される出力です。

```json
{
  "bytesToCapturePerPacket": 0,
  "etag": "W/\"b8cf3528-2e14-45cb-a7f3-5712ffb687ac\"",
  "filters": [
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "20"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "80"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "443"
    },
    {
      "localIpAddress": "",
      "localPort": "",
      "protocol": "UDP",
      "remoteIpAddress": "",
      "remotePort": ""
    }
  ],
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/pa
cketCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "provisioningState": "Succeeded",
  "resourceGroup": "NetworkWatcherRG",
  "storageLocation": {
    "filePath": null,
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/gwteststorage123abc",
    "storagePath": "https://gwteststorage123abc.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/{resourceGroupName}/p
roviders/microsoft.compute/virtualmachines/{vmName}/2017/05/25/packetcapture_16_22_34_630.cap"
  },
  "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}",
  "timeLimitInSeconds": 18000,
  "totalBytesPerSession": 1073741824
}
```

## <a name="get-a-packet-capture"></a>パケット キャプチャを取得する

`az network watcher packet-capture show-status` コマンドを実行して、現在実行中または完了したパケット キャプチャの状態を取得します。

```azurecli-interactive
az network watcher packet-capture show-status --name packetCaptureName --location {networkWatcherLocation}
```

次の例は、`az network watcher packet-capture show-status` コマンドからの出力です。 次の例は、StopReason が TimeExceeded でキャプチャが Stopped となった場合の例です。

```
{
  "additionalProperties": {
    "status": "Succeeded"
  },
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "packetCaptureError": [],
  "packetCaptureStatus": "Stopped",
  "stopReason": "TimeExceeded"
}
```

## <a name="stop-a-packet-capture"></a>パケット キャプチャを停止する

`az network watcher packet-capture stop` コマンドを実行することで、キャプチャ セッションが進行中の場合は停止します。

```azurecli-interactive
az network watcher packet-capture stop --name packetCaptureName --location westcentralus
```

> [!NOTE]
> このコマンドレットは、現在実行中のキャプチャ セッションまたは既に停止している既存のセッションに対して実行した場合、応答を返しません。

## <a name="delete-a-packet-capture"></a>パケット キャプチャを削除する

```azurecli-interactive
az network watcher packet-capture delete --name packetCaptureName --location westcentralus
```

> [!NOTE]
> パケット キャプチャを削除しても、ストレージ アカウント内のファイルは削除されません。

## <a name="download-a-packet-capture"></a>パケット キャプチャをダウンロードする

パケット キャプチャ セッションが完了すると、BLOB ストレージまたは VM 上のローカル ファイルにキャプチャ ファイルをアップロードできます。 パケット キャプチャの格納場所は、セッションの作成時に定義されます。 ストレージ アカウントに保存されているこれらのキャプチャ ファイルにアクセスする際の便利なツールが Microsoft Azure Storage Explorer です。このツールは、 https://storageexplorer.com/ からダウンロードできます。

ストレージ アカウントが指定されている場合、パケット キャプチャ ファイルは、次の場所にあるストレージ アカウントに保存されます。

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>次のステップ

[アラートがトリガーするパケット キャプチャの作成](network-watcher-alert-triggered-packet-capture.md)に関するページを参照して、仮想マシンのアラートを使用してパケット キャプチャを自動化する方法を確認する

[IP フロー検証の確認](diagnose-vm-network-traffic-filtering-problem.md)に関する記事を参照して、VM で送受信される特定のトラフィックが許可されているかどうかを調べる

<!-- Image references -->