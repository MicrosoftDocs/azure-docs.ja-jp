---
title: "Azure Network Watcher でパケット キャプチャを管理する - Azure Portal | Microsoft Docs"
description: "このページでは、Azure Portal を使用して、Network Watcher のパケット キャプチャ機能を管理する方法を説明します。"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 59edd945-34ad-4008-809e-ea904781d918
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: c309151e3e3aa6ff235f0dee2ddf975940f34f44
ms.lasthandoff: 03/24/2017

---

# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>ポータルを使用して Azure Network Watcher でパケット キャプチャを管理する

> [!div class="op_single_selector"]
> - [Azure ポータル](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI](network-watcher-packet-capture-manage-cli.md)
> - [REST API](network-watcher-packet-capture-manage-rest.md)

Network Watcher のパケット キャプチャを使用すると、仮想マシンとの間で送受信されるトラフィックを追跡するキャプチャ セッションを作成できます。 必要なトラフィックのみを確実にキャプチャするためにキャプチャ セッション用のフィルターが用意されています。 パケット キャプチャは、事後と事前に、ネットワークの異常を診断するのに役立ちます。 その他の用途には、ネットワーク統計の収集、ネットワークへの侵入に関する情報を取得などがあり、クライアント サーバー間の通信のデバッグなどに役立ちます。 パケット キャプチャをリモートでトリガーすることができることで、この機能によってパケット キャプチャを手動で、目的のマシン上で実行する負荷が軽減されて、貴重な時間の節約になります。

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

この記事では、パケット キャプチャで現在利用できるさまざまな管理作業を見ていきます。

- [**パケット キャプチャを開始する**](#start-a-packet-capture)
- [**パケット キャプチャを停止する**](#stop-a-packet-capture)
- [**パケット キャプチャを削除する**](#delete-a-packet-capture)
- [**パケット キャプチャをダウンロードする**](#download-a-packet-capture)

## <a name="before-you-begin"></a>開始する前に

この記事では、次のリソースがあることを前提としています。

- パケット キャプチャを作成するリージョン内の Network Watcher インスタンス
- パケット キャプチャ拡張機能が有効になっている仮想マシン

> [!IMPORTANT]
> パケット キャプチャには `AzureNetworkWatcherExtension` 仮想マシン拡張機能が必要です。 Windows VM の拡張機能のインストールについては、[Windows 用 Azure Network Watcher Agent 仮想マシン拡張機能](../virtual-machines/virtual-machines-windows-extensions-nwa.md)に関する記事を参照してください。Linux VM の場合は、[Linux 用 Azure Network Watcher Agent 仮想マシン拡張機能](../virtual-machines/virtual-machines-linux-extensions-nwa.md)に関する記事を参照してください。

### <a name="packet-capture-agent-extension-through-the-portal"></a>ポータルを通じたパケット キャプチャ エージェント拡張機能のインストール

ポータルを通じてパケット キャプチャ VM エージェントをインストールするには、仮想マシンに移動し、**[拡張機能]** > **[追加]** の順にクリックして、**Windows 用 Network Watcher Agent** を検索して探します。

![エージェント ビュー][agent]

## <a name="packet-capture-overview"></a>パケット キャプチャの概要

[Azure Portal](https://portal.azure.com) に移動し、**[ネットワーク]** > **[Network Watcher]** > **[パケット キャプチャ]** の順にクリックします。

[概要] ページでは、存在するすべてのパケット キャプチャが、状態に関係なく一覧で表示されます。

> [!NOTE]
> パケット キャプチャには、ポート 443 を経由するストレージ アカウントへの接続が必要です。

![パケット キャプチャの概要画面][1]

## <a name="start-a-packet-capture"></a>パケット キャプチャを開始する

**[追加]** をクリックしてパケット キャプチャを作成します。

パケット キャプチャに定義できるプロパティは次のとおりです。

**主な設定**

- **[サブスクリプション]** - この値は、使用されているサブスクリプションです。各サブスクリプションで、Network Watcher インスタンスが必要です。
- **[リソース グループ]** - 対象となっている仮想マシンのリソース グループです。
- **[ターゲット仮想マシン]** - パケット キャプチャを実行している仮想マシンです。
- **[パケット キャプチャ名]** - この値は、パケット キャプチャの名前です。

**キャプチャの構成**

- **[ストレージ アカウント]** - パケット キャプチャをストレージ アカウントに保存するかどうかを決定します。
- **[ファイル]** - パケット キャプチャをローカルの仮想マシンに保存するかどうかを決定します。
- **[ストレージ アカウント]** - パケット キャプチャの保存先として選択されたストレージ アカウントです。 既定の保存先は、https://{storage account name}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscription id}/resourcegroups/{resource group name}/providers/microsoft.compute/virtualmachines/{virtual machine name}/{YY}/{MM}/{DD}/packetcapture_{HH}_{MM}_{SS}_{XXX}.cap です  (**[ストレージ アカウント]** を選択した場合にのみ有効です)。
- **[ローカル ファイル パス]** - パケット キャプチャを保存する、仮想マシン上のローカル パスです  (**[ファイル]** を選択した場合にのみ有効です)。 有効なパスを指定する必要があります。
- **[1 パケットあたりの最大バイト数]** - 各パケットからキャプチャされるバイト数です。空白のままの場合、すべてのバイトがキャプチャされます。
- **[1 セッションあたりの最大バイト数]** - キャプチャされるバイト数の合計値です。この値に達すると、パケット キャプチャが停止します。
- **[制限時間 (秒)]** - パケット キャプチャを停止するまでの制限時間を設定します。 既定値は 1,800 秒です。

> [!NOTE]
> Premium Storage アカウントは、現在、パケット キャプチャの格納には対応していません。

**フィルター処理 (省略可能)**

- **[プロトコル]** - パケット キャプチャをフィルター処理するためのプロトコルです。 使用可能な値は、[TCP]、[UDP]、[任意] のいずれかです。
- **[ローカル IP アドレス]** - この値でパケット キャプチャをフィルター処理すると、ローカル IP アドレスがこのフィルターの値と一致するパケットだけが出力されます。
- **[ローカル ポート]** - この値でパケット キャプチャをフィルター処理すると、ローカル ポートがこのフィルターの値と一致するパケットだけが出力されます。
- **[リモート IP アドレス]** - この値でパケット キャプチャをフィルター処理すると、リモート IP アドレスがこのフィルターの値と一致するパケットだけが出力されます。
- **[リモート ポート]** - この値でパケット キャプチャをフィルター処理すると、リモート ポートがこのフィルターの値と一致するパケットだけが出力されます。

> [!NOTE]
> ポートと IP アドレスの値には、1 つの値、値の範囲、または値のセットを指定できます  (つまり、ポートは 80 ～ 1024)。必要なフィルターをいくつでも定義することができます。

値を入力したら、**[OK]** をクリックし、パケット キャプチャを作成します。

![パケット キャプチャを作成する][2]

パケット キャプチャに設定した制限時間が経過すると、パケット キャプチャは停止され、確認が可能になります。 パケット キャプチャ セッションは、手動で停止することもできます。

## <a name="delete-a-packet-capture"></a>パケット キャプチャを削除する

パケット キャプチャ ビューで、**コンテキスト メニュー** (...) をクリックするか右クリックし、**[削除]** をクリックしてパケット キャプチャを停止します。

![パケット キャプチャを削除する][3]

> [!NOTE]
> パケット キャプチャを削除しても、ストレージ アカウント内のファイルは削除されません。

パケット キャプチャを削除するか確認を求められたら、**[はい]** をクリックします。

![確認][4]

## <a name="stop-a-packet-capture"></a>パケット キャプチャを停止する

パケット キャプチャ ビューで、**コンテキスト メニュー** (...) をクリックするか右クリックし、**[停止]** をクリックしてパケット キャプチャを停止します。

## <a name="download-a-packet-capture"></a>パケット キャプチャをダウンロードする

パケット キャプチャ セッションが完了すると、キャプチャ ファイルは Blob Storage または VM 上のローカル ファイルにアップロードされます。 パケット キャプチャの格納場所は、セッションの作成時に定義されます。 ストレージ アカウントに対して保存されているこれらのキャプチャ ファイルにアクセスする便利なツールが、http://storageexplorer.com/ からダウンロードできる Microsoft Azure ストレージ エクスプローラーです。

ストレージ アカウントが指定されている場合、パケット キャプチャ ファイルは、次の場所にあるストレージ アカウントに保存されます。
```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>次のステップ

[アラートがトリガーするパケット キャプチャの作成](network-watcher-alert-triggered-packet-capture.md)に関するページを参照して、仮想マシンのアラートを使用してパケット キャプチャを自動化する方法を確認する

[IP フロー検証の確認](network-watcher-check-ip-flow-verify-portal.md)に関する記事を参照して、VM で送受信される特定のトラフィックが許可されているかどうかを調べる

<!-- Image references -->
[1]: ./media/network-watcher-packet-capture-manage-portal/figure1.png
[2]: ./media/network-watcher-packet-capture-manage-portal/figure2.png
[3]: ./media/network-watcher-packet-capture-manage-portal/figure3.png
[4]: ./media/network-watcher-packet-capture-manage-portal/figure4.png
[agent]: ./media/network-watcher-packet-capture-manage-portal/agent.png














