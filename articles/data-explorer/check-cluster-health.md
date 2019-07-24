---
title: Azure のデータ エクスプ ローラー クラスターの正常性を確認する
description: この記事では、Azure のデータ エクスプローラー クラスターの正常性を監視する手順について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a280d8869a3790444a97c38f792a3d9eeb6bde1d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59794663"
---
# <a name="check-the-health-of-an-azure-data-explorer-cluster"></a>Azure のデータ エクスプ ローラー クラスターの正常性を確認する

Azure のデータ エクスプ ローラー クラスターの正常性に影響をする要素は CPU やメモリ、ディスク サブシステムなどいくつかあります｡ この記事では、クラスターの正常性を評価するために行えるいくつかの基本的な手順を示しています。

1. [https://dataexplorer.azure.com](https://dataexplorer.azure.com) にサインインします。

1. 左側のウィンドウでクラスターを選択して、次のコマンドを実行します。

    ```Kusto
    .show diagnostics
    | project IsHealthy
    ```
    出力が 1 なら正常です。0 の場合は問題があります。

1. [Azure ポータル](https://portal.azure.com)にサインインし、IoT Hub に移動します。

1. **監視** で **メトリクス**を選択し、次の図に示すように **Keep Alive** を選択します｡ 出力が 1 に近ければ、クラスターが正常であることを示します｡

    ![クラスターの Keep Alive メトリック](media/check-cluster-health/portal-metrics.png)

1. グラフに他のメトリックを追加することができます。 グラフを選択し、**[メトリックの追加]** を選択します。 別のメトリックを選択します。この例では **CPU** が表示されます。

    ![メトリックを追加する](media/check-cluster-health/add-metric.png)

1. クラスターの正常性診断で問題が起きた場合は､[Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) でサポート リクエストを開いてください｡