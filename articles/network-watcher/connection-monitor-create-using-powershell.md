---
title: 接続モニターを作成する - PowerShell
titleSuffix: Azure Network Watcher
description: PowerShell を使用して接続モニターを作成する方法について説明します。
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: 7f175d82b650871437a506ea4513f0ae28360f68
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99833033"
---
# <a name="create-a-connection-monitor-by-using-powershell"></a>PowerShell を使用して接続モニターを作成する

> [!IMPORTANT]
> 2021 年 7 月 1 日以降、既存のワークスペースに新しいテストを追加したり、Network Performance Monitor で新しいワークスペースを有効にしたりできなくなります。 接続モニター (クラシック) に新しい接続モニターを追加することもできなくなります。 2021 年 7 月 1 日より前に作成されたテストおよび接続モニターは引き続き使用することができます。 現在のワークロードに対するサービスの中断を最小限に抑えるには、2024 年 2 月 29 日より前に、[Network Performance Monitor から](migrate-to-connection-monitor-from-network-performance-monitor.md)、または[接続モニター (クラシック) から](migrate-to-connection-monitor-from-connection-monitor-classic.md) Azure Network Watcher の新しい接続モニターにテストを移行します。


Azure Network Watcher の接続モニター機能を使用して、リソース間の通信を監視する方法について説明します。


## <a name="before-you-begin"></a>開始する前に

接続モニターを使用して作成する接続モニターでは、オンプレミスのマシンと Azure 仮想マシン (VM) の両方をソースとして追加できます。 これらの接続モニターでは、エンドポイントへの接続も監視できます。 エンドポイントは、Azure または他の任意の URL や IP 上のどこにあってもかまいません。

接続モニターには、次のエンティティが含まれています。

* **接続モニター リソース**:リージョン固有の Azure リソース。 以下のすべてのエンティティが、接続モニター リソースのプロパティです。
* **エンドポイント**: 接続チェックに含まれるソースまたはターゲット。 エンドポイントの例としては、Azure VM、オンプレミスのエージェント、URL、IP などがあります。
* **テスト構成**:テスト用のプロトコル固有の構成。 選択したプロトコルに基づいて、ポート、しきい値、テストの頻度、その他のパラメーターを定義できます。
* **テスト グループ**:ソース エンドポイント、ターゲット エンドポイント、テスト構成が含まれるグループ。 接続モニターには、複数のテスト グループを含めることができます。
* **テスト**:ソース エンドポイント、ターゲット エンドポイント、テスト構成の組み合わせ。 テストは、監視データを入手できる最も小さいレベルです。 監視データには、失敗したチェックの割合と、ラウンドトリップ時間 (RTT) が含まれます。

    ![テスト グループとテストの関係が定義されている接続モニターを示す図。](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-a-connection-monitor"></a>接続モニターを作成する手順

PowerShell を使用して接続モニターを作成するには、次のコマンドを使用します。

```powershell

//Connect to your Azure account with the subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId <your-subscription>
//Select region
$nw = "NetworkWatcher_centraluseuap"
//Declare endpoints like Azure VM below. You can also give VNET,Subnet,Log Analytics workspace
$sourcevmid1 = New-AzNetworkWatcherConnectionMonitorEndpointObject -Name MyAzureVm -ResourceID /subscriptions/<your-subscription>/resourceGroups/<your resourceGroup>/providers/Microsoft.Compute/virtualMachines/<vm-name>
//Declare endpoints like URL, IPs
$bingEndpoint = New-AzNetworkWatcherConnectionMonitorEndpointObject -name Bing -Address www.bing.com # Destination URL
//Create test configuration.Choose Protocol and parametersSample configs below.

$IcmpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -IcmpProtocol
$TcpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -TcpProtocol -Port 80
$httpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -HttpProtocol -Port 443 -Method GET -RequestHeader @{Allow = "GET"} -ValidStatusCodeRange 2xx, 300-308 -PreferHTTPS
$httpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name http-tc -TestFrequencySec 60 -ProtocolConfiguration $httpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
$icmpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name icmp-tc -TestFrequencySec 30 -ProtocolConfiguration $icmpProtocolConfiguration -SuccessThresholdChecksFailedPercent 5 -SuccessThresholdRoundTripTimeMs 500
$tcpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name tcp-tc -TestFrequencySec 60 -ProtocolConfiguration $TcpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
//Create Test Group
$testGroup1 = New-AzNetworkWatcherConnectionMonitorTestGroupObject -Name testGroup1 -TestConfiguration $httpTestConfiguration, $tcpTestConfiguration, $icmpTestConfiguration -Source $sourcevmid1 -Destination $bingEndpoint,
$testname = "cmtest9"
//Create Connection Monitor
New-AzNetworkWatcherConnectionMonitor -NetworkWatcherName $nw -ResourceGroupName NetworkWatcherRG -Name $testname -TestGroup $testGroup1

```

## <a name="description-of-properties"></a>プロパティの説明

* **ConnectionMonitorName**:接続モニター リソースの名前です。

* **SUB**:接続モニターの作成先となるサブスクリプションのサブサブスクリプション ID。

* **NW**:接続モニターが作成される Network Watcher のリソース ID。

* **[場所]** :接続モニターが作成されるリージョン。

* **エンドポイント**
    * **[名前]** : 各エンドポイントの一意の名前。
    * **[リソース ID]** : Azure エンドポイントの場合、リソース ID は VM の Azure Resource Manager リソース ID を示します。 Azure 以外のエンドポイントの場合、リソース ID は、Azure 以外のエージェントにリンクされている Log Analytics ワークスペースの Azure Resource Manager リソース ID を示します。
    * **Address**:リソース ID が指定されていない場合、またはリソース ID が Log Analytics ワークスペース内にある場合にのみ適用されます。 リソース ID なしで使用する場合、これには任意のパブリック エンドポイントの URL または IP を指定できます。 Log Analytics リソース ID と共に使用する場合、これは監視エージェントの FQDN を示します。
    * **フィルター**:Azure 以外のエンドポイントの場合は、フィルターを使用して、接続モニター リソースの Log Analytics ワークスペースから監視エージェントを選択します。 フィルターが設定されていない場合、Log Analytics ワークスペースに属するすべてのエージェントを監視に使用できます。
        * **[種類]** :**エージェント アドレス** として設定します。
        * **Address**:オンプレミスのエージェントの FQDN として設定します。

* **テスト グループ**
    * **[名前]** :テスト グループの名前を指定します。
    * **Sources**:前に作成したエンドポイントから選択します。 Azure ベースのソース エンドポイントには Azure Network Watcher 拡張機能がインストールされている必要があり、Azure 以外をベースとしたソース エンドポイントには Azure Log Analytics エージェントがインストールされている必要があります。 ソース用のエージェントをインストールするには、「[監視エージェントをインストールする](./connection-monitor-overview.md#install-monitoring-agents)」を参照してください。
    * **宛先**:前に作成したエンドポイントから選択します。 Azure VM または任意のエンドポイント (パブリック IP、URL、FQDN) をターゲットとして指定すると、それらへの接続を監視できます。 1 つのテスト グループに、Azure VM、Office 365 URL、Dynamics 365 URL、カスタム エンドポイントを追加できます。
    * **Disable**:テスト グループで指定されているすべてのソースとターゲットに対する監視を無効します。

* **テスト構成**
    * **[名前]** : テスト構成に名前を指定します。
    * **TestFrequencySec**:指定したプロトコルとポートでソースがターゲットに ping を実行する頻度を指定します。 30 秒、1 分、5 分、15 分、30 分のいずれかを選択できます。 ソースでは、選択した値に基づいて、ターゲットへの接続がテストされます。 たとえば、30 秒を選択すると、ソースにより、少なくとも 30 秒に 1 回、ターゲットへの接続が確認されます。
    * **プロトコル**:TCP、ICMP、HTTP、または HTTPS を選択します。 プロトコルによっては、次のプロトコル固有の構成を選択することもできます。
        * **preferHTTPS**:HTTP 経由で HTTPS を使用するかどうかを指定します。
        * **port**:任意の宛先ポートを指定します。
        * **disableTraceRoute**:ソースでのトポロジとホップバイホップ RTT の検出が停止します。 これは、TCP または ICMP を使用したテスト グループに適用されます。
        * **method**:HTTP 要求メソッド (GET または POST) を選択します。 これは、HTTP を使用したテスト構成に適用されます。
        * **path**:URL に追加するパス パラメーターを指定します。
        * **validStatusCodes**:該当する状態コードを選択します。 応答コードが一致しない場合は、診断メッセージが表示されます。
        * **requestHeaders**:宛先に渡されるカスタム要求ヘッダー文字列を指定します。
    * **Success threshold**:以下のネットワーク パラメーターにしきい値を設定します。
        * **checksFailedPercent**:指定した条件で行われるソースによるターゲットへの接続の確認で、失敗してもかまわないチェックの割合を設定します。 TCP または ICMP プロトコルでは、失敗したチェックの割合はパケット損失の割合と同等である可能性があります。 HTTP プロトコルでは、このフィールドは、応答を受け取らなかった HTTP 要求の割合を表します。
        * **roundTripTimeMs**:テスト構成でソースがターゲットに接続するのにかかる時間をミリ秒で設定します。

## <a name="scale-limits"></a>スケールの上限

接続モニターには、次のようなスケール制限があります。

* 1 つのリージョンの 1 つのサブスクリプションあたりの接続モニターの最大数: 100
* 接続モニターあたりのテスト グループの最大数: 20
* 接続モニターあたりのソースとターゲットの最大数: 100
* 接続モニターあたりのテスト構成の最大数:

## <a name="next-steps"></a>次のステップ

* [監視データを分析してアラートを設定する方法](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts)を確認する。
* [ネットワークの問題を診断する方法](./connection-monitor-overview.md#diagnose-issues-in-your-network)を確認する。
