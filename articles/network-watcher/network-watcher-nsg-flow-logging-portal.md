---
title: "Azure Network Watcher を使用したネットワーク セキュリティ グループのフローのログの管理 | Microsoft Docs"
description: "このページでは、Azure Network Watcher でネットワーク セキュリティ グループのフローのログを管理する方法を説明します。"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: d716f11818bc0ad6dd9e5f93951b011dd6774c7b
ms.lasthandoff: 03/22/2017


---

# <a name="manage-network-security-group-flow-logs-in-the-azure-portal"></a>Azure Portal でのネットワーク セキュリティ グループのフローのログの管理

> [!div class="op_single_selector"]
> - [Azure ポータル](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

ネットワーク セキュリティ グループのフローのログは、ネットワーク セキュリティ グループを使用したイングレスおよびエグレス IP トラフィックに関する情報を表示できる Network Watcher の機能です。 これらのフローのログは json 形式で記述され、ルールごとの送信フローと受信フロー、フローが適用される NIC、フローに関する 5 組の情報 (送信元/宛先 IP、送信元/宛先ポート、プロトコル)、トラフィックが許可されているか拒否されているかが示されます。

## <a name="before-you-begin"></a>開始する前に

このシナリオは、[Network Watcher の作成](network-watcher-create.md)に関する記事の手順に従って Network Watcher を作成済みであることを前提としています。 また、有効な仮想マシンがあるリソース グループを使用することも前提としています。

## <a name="enable-flow-logs"></a>フローのログを有効にする

ネットワーク セキュリティ グループのフローのログを有効にするには、次の手順に従います。

### <a name="step-1"></a>手順 1

Network Watcher インスタンスに移動し、**[フローのログ]** を選択します。

![フローのログの概要][1]

### <a name="step-2"></a>手順 2.

一覧でネットワーク セキュリティ グループをクリックして選択します。

![フローのログの概要][2]

### <a name="step-3"></a>手順 3. 

**[フローのログ設定]** ブレードで、状態を **[オン]** に設定し、ストレージ アカウントを構成します。  完了したら **[OK]** をクリックし、**[保存]** をクリックします。

![フローのログの概要][3]

## <a name="download-flow-logs"></a>フローのログをダウンロードする

フローのログはストレージ アカウントに保存されます。 フローのログを表示するには、それらのログをダウンロードする必要があります。

### <a name="step-1"></a>手順 1

フローのログをダウンロードするには、**[構成済みのストレージ アカウントからフローのログをダウンロードできました。]** をクリックします。  これにより、ストレージ アカウント ビューが表示されます。ここで、ダウンロードするログに移動できます。

![フローのログ設定][4]

### <a name="step-2"></a>手順 2.

適切なストレージ アカウントに移動し、**[コンテナー]** > **[insights-log-networksecuritygroupflowevent]** をクリックします。

![フローのログ設定][5]

### <a name="step-3"></a>手順 3.

フローのログの場所にドリルダウンし、フローのログを選択して **[ダウンロード]** をクリックします。

![フローのログ設定][6]

ログの構造については、[ネットワーク セキュリティ グループのフローのログの概要](network-watcher-nsg-flow-logging-overview.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ

[PowerBI を使用して、NSG フロー ログを視覚化する](network-watcher-visualize-nsg-flow-logs-power-bi.md)方法を確認する

<!-- Image references -->
[1]: ./media/network-watcher-nsg-flow-logging-portal/figure1.png
[2]: ./media/network-watcher-nsg-flow-logging-portal/figure2.png
[3]: ./media/network-watcher-nsg-flow-logging-portal/figure3.png
[4]: ./media/network-watcher-nsg-flow-logging-portal/figure4.png
[5]: ./media/network-watcher-nsg-flow-logging-portal/figure5.png
[6]: ./media/network-watcher-nsg-flow-logging-portal/figure6.png

