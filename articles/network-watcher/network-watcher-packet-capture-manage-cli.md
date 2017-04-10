---
title: "Azure Network Watcher でパケット キャプチャを管理する - Azure CLI | Microsoft Docs"
description: "このページでは、Azure CLI を使用して Network Watcher のパケット キャプチャ機能を管理する方法を説明する"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: e2f37514cc2cc45192783d2f542320879016df37
ms.lasthandoff: 03/28/2017

---

# <a name="manage-packet-captures-with-azure-network-watcher-using-azure-cli"></a>Azure CLI を使用して Azure Network Watcher でパケット キャプチャを管理する

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

Network Watcher のパケット キャプチャを使用すると、仮想マシンとの間で送受信されるトラフィックを追跡するキャプチャ セッションを作成できます。 必要なトラフィックのみを確実にキャプチャするためにキャプチャ セッション用のフィルターが用意されています。 パケット キャプチャは、事後と事前に、ネットワークの異常を診断するのに役立ちます。 その他の用途には、ネットワーク統計の収集、ネットワークへの侵入に関する情報を取得などがあり、クライアント サーバー間の通信のデバッグなどに役立ちます。 パケット キャプチャをリモートでトリガーすることができることで、この機能によってパケット キャプチャを手動で、目的のマシン上で実行する負荷が軽減されて、貴重な時間の節約になります。

この記事では、Windows、Mac、Linux で使用できるクロスプラット フォーム Azure CLI 1.0 を使います。 Network Watcher では、CLI サポートの Azure CLI 1.0 が使用されています。

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

この記事では、パケット キャプチャで現在利用できるさまざまな管理作業を見ていきます。

- [**パケット キャプチャを開始する**](#start-a-packet-capture)
- [**パケット キャプチャを停止する**](#stop-a-packet-capture)
- [**パケット キャプチャを削除する**](#delete-a-packet-capture)
- [**パケット キャプチャをダウンロードする**](#download-a-packet-capture)

## <a name="before-you-begin"></a>開始する前に

この記事では、次のリソースがあることを前提としています。

- パケット キャプチャを作成するリージョン内の Network Watcher インスタンス
- パケット キャプチャ拡張機能が有効になっている仮想マシン。

> [!IMPORTANT]
> パケット キャプチャを仮想マシン上で実行するにはエージェントが必要です。 エージェントは、拡張機能としてインストールされます。 VM 拡張機能については、[仮想マシン拡張機能とその機能](../virtual-machines/virtual-machines-windows-extensions-features.md)に関するページを参照してください。

## <a name="install-vm-extension"></a>VM 拡張機能をインストールする

### <a name="step-1"></a>手順 1

ゲスト仮想マシンにパケット キャプチャ エージェントをインストールするには、`azure vm extension set` コマンドレットを実行します。

Windows 仮想マシンの場合:

```azurecli
azure vm extension set -g resourceGroupName -m virtualMachineName -p Microsoft.Azure.NetworkWatcher -r AzureNetworkWatcherExtension -n NetworkWatcherAgentWindows -o 1.4
```

Linux 仮想マシンの場合:

```azurecli
azure vm extension set -g resourceGroupName -m virtualMachineName -p Microsoft.Azure.NetworkWatcher -r AzureNetworkWatcherExtension -n NetworkWatcherAgentLinux -o 1.4
````

### <a name="step-2"></a>手順 2.

エージェントがインストールされていることを確認するために、リソース グループと仮想マシン名を渡して `vm extension get` コマンドレットを実行します。 結果のリストを調べて、エージェントがインストールされていることを確認します。

```azurecli
azure vm extension get -g resourceGroupName -m virtualMachineName
```

以下のサンプルは、`azure vm extension get` の実行に対する応答の例です。

```
info:    Executing command vm extension get
+ Looking up the VM "virtualMachineName"
data:    Publisher                       Name                     Version  State
data:    ------------------------------  -----------------------  -------  ---------
data:    Microsoft.Azure.NetworkWatcher  NetworkWatcherAgentTest  1.4      Succeeded
info:    vm extension get command OK
```

## <a name="start-a-packet-capture"></a>パケット キャプチャを開始する

上記の手順が完了すると、パケット キャプチャ エージェントは仮想マシンにインストールされます。

### <a name="step-1"></a>手順 1

次の手順では、Network Watcher インスタンスを取得します。 この変数は、手順 4 で `network watcher show` コマンドレットに渡されます。

```azurecli
azure network watcher show -g resourceGroup -n networkWatcherName
```

### <a name="step-2"></a>手順 2.

ストレージ アカウントを取得します。 このストレージ アカウントは、パケット キャプチャ ファイルの格納に使用されます。

```azurecli
azure storage account list
```

### <a name="step-3"></a>手順 3.

フィルターを使用して、パケット キャプチャによって格納されるデータを制限できます。 次の例では、いくつかのフィルターを使用してパケット キャプチャを設定します。  最初の 3 つのフィルターは、ローカル IP 10.0.0.3 から宛先ポート 20、80、443 への発信 TCP トラフィックのみを収集します。  最後のフィルターは、UDP トラフィックのみを収集します。

```azurecli
azure network watcher packet-capture create -g resourceGroupName -w networkWatcherName -n packetCaptureName -t targetResourceId -o storageAccountResourceId -f "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

単一のパケット キャプチャに対して複数のフィルターを定義できます。 複雑なフィルター構造を使用している場合は、構文エラーを回避するために json ファイルとしてフィルターを使用することをお勧めします。 たとえば、フラグ "-f" の代わりに "-r" を使用して、次のフィルターを含む json ファイルの場所を渡します。

```json
[
    {
        "protocol":"TCP",
        "remoteIPAddress":"1.1.1.1-255.255.255",
        "localIPAddress":"10.0.0.3",
        "remotePort":"20"
    },
    {
        "protocol":"TCP",
        "remoteIPAddress":"1.1.1.1-255.255.255",
        "localIPAddress":"10.0.0.3",
        "remotePort":"80"
    },
    {
        "protocol":"TCP",
        "remoteIPAddress":"1.1.1.1-255.255.255",
        "localIPAddress":"10.0.0.3",
        "remotePort":"443"
    },
    {
        "protocol":"UDP"
    }
]
```


次の例は、`network watcher packet-capture create` コマンドレットを実行したときに予想される出力です。

```
data:    Name                            : packetCaptureName
data:    Etag                            : W/"d59bb2d2-dc95-43da-b740-e0ef8fcacecb"
data:    Target                          : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Compute/virtualMachines/testVM
data:    Bytes To Capture Per Packet     : 0
data:    Total Bytes Per Session         : 1073741824
data:    Time Limit In Seconds           : 18000
data:    Storage Location:
data:      Storage Id                    : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/testStorage
data:      Storage Path                  : https://testStorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/testRG/providers/microsoft.compute/virtualmachines/testVM/2017/02/17/packetcapture_01_21_18_145.cap
data:    Filters                         : []
data:    Provisioning State              : Succeeded
info:    network watcher packet-capture create command OK
```

## <a name="get-a-packet-capture"></a>パケット キャプチャを取得する

`network watcher packet-capture show` コマンドレットを実行して、現在実行中または完了したパケット キャプチャの状態を取得します。

```azurecli
azure network watcher packet-capture show -g resourceGroupName -w networkWatcherName -n packetCaptureName
```

次の例は、`network watcher packet-capture show` コマンドレットからの出力です。 次の例はキャプチャ完了後です。 PacketCaptureStatus 値は Stopped で、StopReason は TimeExceeded です。 この値は、パケット キャプチャが成功し、所定の時間実行されたことを示します。

```
data:    Name                            : packetCaptureName
data:    Etag                            : W/"d59bb2d2-dc95-43da-b740-e0ef8fcacecb"
data:    Target                          : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Compute/virtualMachines/testVM
data:    Bytes To Capture Per Packet     : 0
data:    Total Bytes Per Session         : 1073741824
data:    Time Limit In Seconds           : 18000
data:    Storage Location:
data:      Storage Id                    : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/testStorage
data:      Storage Path                  : https://testStorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/testRG/providers/microsoft.compute/virtualmachines/testVM/2017/02/17/packetcapture_01_21_18_145.cap
data:    Filters                         : []
data:    Provisioning State              : Succeeded
info:    network watcher packet-capture show command OK
```

## <a name="stop-a-packet-capture"></a>パケット キャプチャを停止する

`network watcher packet-capture stop` コマンドレットを実行することで、キャプチャ セッションが進行中の場合は停止します。

```azurecli
azure network watcher packet-capture stop -g resourceGroupName -w networkWatcherName -n packetCaptureName
```

> [!NOTE]
> このコマンドレットは、現在実行中のキャプチャ セッションまたは既に停止している既存のセッションに対して実行した場合、応答を返しません。

## <a name="delete-a-packet-capture"></a>パケット キャプチャを削除する

```azurecli
azure network watcher packet-capture delete -g resourceGroupName -w networkWatcherName -n packetCaptureName
```

> [!NOTE]
> パケット キャプチャを削除しても、ストレージ アカウント内のファイルは削除されません。

## <a name="download-a-packet-capture"></a>パケット キャプチャをダウンロードする

パケット キャプチャ セッションが完了すると、BLOB ストレージまたは VM 上のローカル ファイルにキャプチャ ファイルをアップロードできます。 パケット キャプチャの格納場所は、セッションの作成時に定義されます。 ストレージ アカウントに対して保存されているこれらのキャプチャ ファイルにアクセスする便利なツールが、http://storageexplorer.com/ からダウンロードできる Microsoft Azure ストレージ エクスプローラーです。

ストレージ アカウントが指定されている場合、パケット キャプチャ ファイルは、次の場所にあるストレージ アカウントに保存されます。

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>次のステップ

[アラートがトリガーするパケット キャプチャの作成](network-watcher-alert-triggered-packet-capture.md)に関するページを参照して、仮想マシンのアラートを使用してパケット キャプチャを自動化する方法を確認する

[IP フロー検証の確認](network-watcher-check-ip-flow-verify-portal.md)に関する記事を参照して、VM で送受信される特定のトラフィックが許可されているかどうかを調べる

<!-- Image references -->

