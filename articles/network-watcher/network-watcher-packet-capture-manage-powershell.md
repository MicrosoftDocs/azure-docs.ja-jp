---
title: パケット キャプチャを管理する - Azure PowerShell
titleSuffix: Azure Network Watcher
description: このページでは、PowerShell を使用して Network Watcher のパケット キャプチャ機能を管理する方法を説明します。
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 8779381425d4180cc7320f251de9c2cfdd1f0510
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99223425"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-powershell"></a>PowerShell を使用して Azure Network Watcher でパケット キャプチャを管理する

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

Network Watcher のパケット キャプチャを使用すると、仮想マシンとの間で送受信されるトラフィックを追跡するキャプチャ セッションを作成できます。 必要なトラフィックのみを確実にキャプチャするためにキャプチャ セッション用のフィルターが用意されています。 パケット キャプチャは、事後と事前に、ネットワークの異常を診断するのに役立ちます。 その他の用途には、ネットワーク統計の収集、ネットワークへの侵入に関する情報を取得などがあり、クライアント サーバー間の通信のデバッグなどに役立ちます。 パケット キャプチャをリモートでトリガーすることができることで、この機能によってパケット キャプチャを手動で、目的のマシン上で実行する負荷が軽減されて、貴重な時間の節約になります。

この記事では、パケット キャプチャで現在利用できるさまざまな管理作業について説明します。

- [**パケット キャプチャを開始する**](#start-a-packet-capture)
- [**パケット キャプチャを停止する**](#stop-a-packet-capture)
- [**パケット キャプチャを削除する**](#delete-a-packet-capture)
- [**パケット キャプチャをダウンロードする**](#download-a-packet-capture)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>開始する前に

この記事では、次のリソースがあることを前提としています。

* パケット キャプチャを作成するリージョン内の Network Watcher インスタンス

* パケット キャプチャ拡張機能が有効になっている仮想マシン

> [!IMPORTANT]
> パケット キャプチャには仮想マシン拡張機能 `AzureNetworkWatcherExtension` が必要です。 Windows VM への拡張機能のインストールについては、[Windows 用 Azure Network Watcher Agent 仮想マシン拡張機能](../virtual-machines/extensions/network-watcher-windows.md)に関する記事をご覧ください。Linux VM の場合は、[Linux 用 Azure Network Watcher Agent 仮想マシン拡張機能](../virtual-machines/extensions/network-watcher-linux.md)に関する記事をご覧ください。

## <a name="install-vm-extension"></a>VM 拡張機能をインストールする

### <a name="step-1"></a>手順 1

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name VM1
```

### <a name="step-2"></a>手順 2.

次の例では、`Set-AzVMExtension` コマンドレットを実行するために必要な拡張機能情報を取得します。 このコマンドレットにより、ゲスト仮想マシンにパケット キャプチャ エージェントがインストールされます。

> [!NOTE]
> `Set-AzVMExtension` コマンドレットは、完了までに数分かかる場合があります。

Windows 仮想マシンの場合:

```powershell
$AzureNetworkWatcherExtension = Get-AzVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentWindows -Version 1.4.585.2
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

Linux 仮想マシンの場合:

```powershell
$AzureNetworkWatcherExtension = Get-AzVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentLinux -Version 1.4.13.0
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

次の例は、`Set-AzVMExtension` コマンドレットの実行後の正常な応答を示しています。

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
```

### <a name="step-3"></a>手順 3.

エージェントがインストールされていることを確認するには、`Get-AzVMExtension` コマンドレットを実行し、仮想マシン名と拡張機能名を渡します。

```powershell
Get-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -VMName $VM.Name -Name $ExtensionName
```

以下のサンプルは、`Get-AzVMExtension` の実行に対する応答の例です。

```
ResourceGroupName       : testrg
VMName                  : testvm1
Name                    : AzureNetworkWatcherExtension
Location                : westcentralus
Etag                    : null
Publisher               : Microsoft.Azure.NetworkWatcher
ExtensionType           : NetworkWatcherAgentWindows
TypeHandlerVersion      : 1.4
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1/
                          extensions/AzureNetworkWatcherExtension
PublicSettings          : 
ProtectedSettings       : 
ProvisioningState       : Succeeded
Statuses                : 
SubStatuses             : 
AutoUpgradeMinorVersion : True
ForceUpdateTag          : 
```

## <a name="start-a-packet-capture"></a>パケット キャプチャを開始する

上記の手順が完了すると、パケット キャプチャ エージェントは仮想マシンにインストールされます。

### <a name="step-1"></a>手順 1

次の手順では、Network Watcher インスタンスを取得します。 この変数は、手順 4 で `New-AzNetworkWatcherPacketCapture` コマンドレットに渡されます。

```powershell
$networkWatcher = Get-AzNetworkWatcher  | Where {$_.Location -eq "westcentralus" }
```

### <a name="step-2"></a>手順 2.

ストレージ アカウントを取得します。 このストレージ アカウントは、パケット キャプチャ ファイルの格納に使用されます。

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName testrg -Name testrgsa123
```

### <a name="step-3"></a>手順 3.

フィルターを使用して、パケット キャプチャによって格納されるデータを制限できます。 次の例では、2 つのフィルターを設定します。  最初のフィルターは、ローカル IP 10.0.0.3 から宛先ポート 20、80、443 への送信 TCP トラフィックのみを収集します。  2 番目のフィルターは、UDP トラフィックのみを収集します。

```powershell
$filter1 = New-AzPacketCaptureFilterConfig -Protocol TCP -RemoteIPAddress "1.1.1.1-255.255.255.255" -LocalIPAddress "10.0.0.3" -LocalPort "1-65535" -RemotePort "20;80;443"
$filter2 = New-AzPacketCaptureFilterConfig -Protocol UDP
```

> [!NOTE]
> 単一のパケット キャプチャに対して複数のフィルターを定義できます。

### <a name="step-4"></a>手順 4.

`New-AzNetworkWatcherPacketCapture` コマンドレットを実行してパケット キャプチャ プロセスを開始し、前の手順で取得した必要な値を渡します。
```powershell

New-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $vm.Id -PacketCaptureName "PacketCaptureTest" -StorageAccountId $storageAccount.id -TimeLimitInSeconds 60 -Filter $filter1, $filter2
```

次の例は、`New-AzNetworkWatcherPacketCapture` コマンドレットを実行したときに予想される出力です。

```
Name                    : PacketCaptureTest
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatcher
                          s/NetworkWatcher_westcentralus/packetCaptures/PacketCaptureTest
Etag                    : W/"3bf27278-8251-4651-9546-c7f369855e4e"
ProvisioningState       : Succeeded
Target                  : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1
BytesToCapturePerPacket : 0
TotalBytesPerSession    : 1073741824
TimeLimitInSeconds      : 60
StorageLocation         : {
                            "StorageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Storage/storageA
                          ccounts/examplestorage",
                            "StoragePath": "https://examplestorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-00000
                          0000000/resourcegroups/testrg/providers/microsoft.compute/virtualmachines/testvm1/2017/02/01/packetcapture_22_42_48_238.cap"
                          }
Filters                 : [
                            {
                              "Protocol": "TCP",
                              "RemoteIPAddress": "1.1.1.1-255.255.255",
                              "LocalIPAddress": "10.0.0.3",
                              "LocalPort": "1-65535",
                              "RemotePort": "20;80;443"
                            },
                            {
                              "Protocol": "UDP",
                              "RemoteIPAddress": "",
                              "LocalIPAddress": "",
                              "LocalPort": "",
                              "RemotePort": ""
                            }
                          ]


```

## <a name="get-a-packet-capture"></a>パケット キャプチャを取得する

`Get-AzNetworkWatcherPacketCapture` コマンドレットを実行して、現在実行中または完了したパケット キャプチャの状態を取得します。

```powershell
Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

次の例は、`Get-AzNetworkWatcherPacketCapture` コマンドレットからの出力です。 次の例はキャプチャ完了後です。 PacketCaptureStatus 値は Stopped で、StopReason は TimeExceeded です。 この値は、パケット キャプチャが成功し、所定の時間実行されたことを示します。
```
Name                    : PacketCaptureTest
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatcher
                          s/NetworkWatcher_westcentralus/packetCaptures/PacketCaptureTest
Etag                    : W/"4b9a81ed-dc63-472e-869e-96d7166ccb9b"
ProvisioningState       : Succeeded
Target                  : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1
BytesToCapturePerPacket : 0
TotalBytesPerSession    : 1073741824
TimeLimitInSeconds      : 60
StorageLocation         : {
                            "StorageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Storage/storageA
                          ccounts/examplestorage",
                            "StoragePath": "https://examplestorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-00000
                          0000000/resourcegroups/testrg/providers/microsoft.compute/virtualmachines/testvm1/2017/02/01/packetcapture_22_42_48_238.cap"
                          }
Filters                 : [
                            {
                              "Protocol": "TCP",
                              "RemoteIPAddress": "1.1.1.1-255.255.255",
                              "LocalIPAddress": "10.0.0.3",
                              "LocalPort": "1-65535",
                              "RemotePort": "20;80;443"
                            },
                            {
                              "Protocol": "UDP",
                              "RemoteIPAddress": "",
                              "LocalIPAddress": "",
                              "LocalPort": "",
                              "RemotePort": ""
                            }
                          ]
CaptureStartTime        : 2/1/2017 10:43:01 PM
PacketCaptureStatus     : Stopped
StopReason              : TimeExceeded
PacketCaptureError      : []
```

## <a name="stop-a-packet-capture"></a>パケット キャプチャを停止する

`Stop-AzNetworkWatcherPacketCapture` コマンドレットを実行することで、キャプチャ セッションが進行中の場合は停止します。

```powershell
Stop-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> このコマンドレットは、現在実行中のキャプチャ セッションまたは既に停止している既存のセッションに対して実行した場合、応答を返しません。

## <a name="delete-a-packet-capture"></a>パケット キャプチャを削除する

```powershell
Remove-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
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
