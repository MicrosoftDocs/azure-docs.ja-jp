---
title: Azure CDN でのリアルタイム統計情報 | Microsoft Docs
description: リアルタイム統計情報は、クライアントにコンテンツを配信するときの Azure CDN のパフォーマンスに関するリアルタイム データを提供します。
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c7989340-1172-4315-acbb-186ba34dd52a
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 3af2e849aa6658e539b0b5bdbda4428cc28e5ce5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84887238"
---
# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Microsoft Azure CDN でのリアルタイム統計情報
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>概要
この文書では、Microsoft Azure CDN のリアルタイム統計情報について説明します。  帯域幅、キャッシュの状態、CDN プロファイルへのコンカレント接続数など、クライアントにコンテンツを配信するときのリアルタイム データは、この機能から得ることができます。 本番稼働イベントを含め、サービスの正常性を随時継続的に監視することができます。

次のグラフを使用できます。

* [帯域幅](#bandwidth)
* [状態コード](#status-codes)
* [キャッシュの状態](#cache-statuses)
* [接続](#connections)

## <a name="accessing-real-time-stats"></a>リアルタイム統計にアクセスする
1. [Azure Portal](https://portal.azure.com) で、CDN プロファイルに移動します。
   
    ![CDN プロファイル ブレード](./media/cdn-real-time-stats/cdn-profile-blade.png)
2. CDN プロファイル ブレードで、 **[管理]** をクリックします。
   
    ![[CDN プロファイル] ブレードの [管理] ボタン](./media/cdn-real-time-stats/cdn-manage-btn.png)
   
    CDN 管理ポータルが開きます。
3. **[分析]** タブにマウス ポインターを合わせ、次に **[Real-Time Stats (リアルタイム統計情報)]** フライアウトにポインターを合わせます。  **[HTTP ラージ オブジェクト]** をクリックします。
   
    ![CDN management portal](./media/cdn-real-time-stats/cdn-premium-portal.png)
   
    リアルタイム統計グラフが表示されます。

それぞれのグラフには、ページの読み込みを起点とし、選択された期間にわたってリアルタイムの統計情報が表示されます。  グラフは、数秒おきに自動的に更新されます。  **[グラフの更新]** ボタンが存在する場合、そのボタンをクリックするとグラフが消去され、その後、選択したデータだけが表示されます。

## <a name="bandwidth"></a>帯域幅
![Bandwidth graph](./media/cdn-real-time-stats/cdn-bandwidth.png)

**帯域幅** グラフには、指定期間に現在のプラットフォームで使用された帯域幅の使用量が表示されます。 グラフの斜線部分は、帯域幅使用量を示します。 帯域幅の正確な使用量は、現在のところ、折れ線グラフの下に直接表示されます。

## <a name="status-codes"></a>状態コード
![Status code graph](./media/cdn-real-time-stats/cdn-status-codes.png)

**[状態コード]** グラフは、選択された期間に特定の HTTP 応答コードが返された頻度を示します。

> [!TIP]
> 各 HTTP 状態コード オプションの詳細については、「 [Azure CDN HTTP Status Codes (Azure CDN HTTP 状態コード)](/previous-versions/azure/mt759238(v=azure.100))」を参照してください。
> 
> 

HTTP 状態コードの一覧は、グラフの上に直接表示されます。 この一覧は、折れ線グラフに追加できる各状態コードとその状態コードの現在の毎秒発生回数を示します。 既定では、グラフの状態コードごとに 1 つの線が表示されます。 ただし、選択すれば、CDN 構成にとって特別な意味のある状態コードのみを監視できます。 そのためには、必要な状態コードを確認し、その他すべてのオプションをオフにしたうえで、 **[グラフの更新]** をクリックします。 

特定の状態コードについて記録されたデータを一時的に非表示にすることもできます。  グラフのすぐ下にある凡例から、非表示にする状態コードをクリックします。 状態コードはすぐにグラフから消えます。 その状態コードをもう一度クリックすると、再び表示されます。

## <a name="cache-statuses"></a>キャッシュの状態
![Cache Statuses graph](./media/cdn-real-time-stats/cdn-cache-status.png)

**[キャッシュの状態]** グラフは、選択された期間に特定の種類のキャッシュ状態が発生した頻度を示します。 

> [!TIP]
> 各キャッシュ状態コード オプションの詳細については、「 [Azure CDN Cache Status Codes (Azure CDN キャッシュ状態コード)](/previous-versions/azure/mt759237(v=azure.100))」を参照してください。
> 
> 

キャッシュの状態コードの一覧は、グラフのすぐ上に表示されます。 この一覧は、折れ線グラフに追加できる各状態コードとその状態コードの現在の毎秒発生回数を示します。 既定では、グラフの状態コードごとに 1 つの線が表示されます。 ただし、選択すれば、CDN 構成にとって特別な意味のある状態コードのみを監視できます。 そのためには、必要な状態コードを確認し、その他すべてのオプションをオフにしたうえで、 **[グラフの更新]** をクリックします。 

特定の状態コードについて記録されたデータを一時的に非表示にすることもできます。  グラフのすぐ下にある凡例から、非表示にする状態コードをクリックします。 状態コードはすぐにグラフから消えます。 その状態コードをもう一度クリックすると、再び表示されます。

## <a name="connections"></a>接続
![Connections graph](./media/cdn-real-time-stats/cdn-connections.png)

このグラフは、エッジ サーバーとの間で確立された接続の数を示しています。 資産に対する要求が CDN を通過するたびに接続が確立されます。

## <a name="next-steps"></a>次の手順
* [Azure CDN のリアルタイム アラート](cdn-real-time-alerts.md)
* [詳細な HTTP レポート](cdn-advanced-http-reports.md)
* [使用量パターン](cdn-analyze-usage-patterns.md)

